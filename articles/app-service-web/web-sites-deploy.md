<properties
	pageTitle="Documentación de implementación del Servicio de aplicaciones de Azure"
	description="Obtenga más información sobre cómo implementar la aplicación en el Servicio de aplicaciones de Azure."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="cephalin;tdykstra"/>
    
# Documentación de implementación del Servicio de aplicaciones de Azure

Este artículo le ayudará a determinar la mejor opción para implementar los archivos de su aplicación web, back-end de aplicación móvil o aplicación de API en el [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) y, a continuación, le dirigirá a los artículos y blogs adecuados con instrucciones de procedimientos específicas de su opción preferida.

## <a name="overview"></a>Información general sobre los procesos de implementación

Tan pronto como cree o aprovisione una aplicación en el Servicio de aplicaciones de Azure, antes de implementar cualquier código en ella, el Servicio de aplicaciones de Azure mantiene el marco de la aplicación por usted (ASP.NET, PHP, Node.js, etc.). Algunos marcos están habilitados de forma predeterminada, mientras que es posible que otros, como Java y Python, necesiten una configuración de marca de verificación sencilla para habilitarlos. Además, puede personalizar el marco de la aplicación, como la versión de PHP o el valor de bits del tiempo de ejecución. Para obtener más información, consulte [Configuración de aplicaciones web en el Servicio de aplicaciones de Azure](web-sites-configure.md).

Como no tiene que preocuparse por el marco de trabajo de la aplicación o del servidor web, la implementación de la aplicación en el Servicio de aplicaciones consiste en implementar código, archivos binarios, archivos de contenido y su estructura de directorios correspondiente en el directorio [**/site/wwwroot** de Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (o el directorio **/Data/Jobs** para WebJobs). El Servicio de aplicaciones admite los tres procesos de implementación siguientes.

- [FTP o FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): use sus FTP o FTPS favoritos habilitados para mover los archivos a Azure, desde [FileZilla](https://filezilla-project.org) hasta IDE completos como [NetBeans](https://netbeans.org). Se trata estrictamente de un proceso de carga de archivos. El Servicio de aplicaciones no proporciona servicios adicionales, como control de versiones, administración de estructura de archivos, etc. 
- [Kudu (Git o Mercurial)](https://github.com/projectkudu/kudu/wiki/Deployment): el [motor de implementación](https://github.com/projectkudu/kudu/wiki) del Servicio de aplicaciones. Inserte el código en Kudu directamente desde cualquier repositorio. Kudu también proporciona servicios agregados siempre que se inserte código en él, como por ejemplo, control de versiones, restauración de paquetes, MSBuild y [enlaces web](https://github.com/projectkudu/kudu/wiki/Web-hooks), para la implementación continua y otras tareas de automatización. Todos estos servicios se pueden personalizar y desencadenar 
    - cada vez que se ejecuta un **git push** desde un repositorio de Git configurado,
	- cada vez que se ejecuta un **hg push** desde un repositorio de Mercurial configurado o 
    - cada vez que un almacenamiento en la nube vinculado, como Dropbox u OneDrive, se sincronice con el Servicio de aplicaciones. 
- [Web Deploy](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): el mismo conjunto de herramientas que automatiza la implementación en servidores de IIS. Implemente código en el Servicio de aplicaciones directamente desde las herramientas de Microsoft que prefiera, como Visual Studio, WebMatrix y Visual Studio Team Services. Esta herramienta es compatible con la implementación de solo diferencias, la creación de bases de datos, las transformaciones de cadenas de conexión, etc. Web Deploy difiere de Kudu en que los archivos binarios de aplicación se compilan antes de implementarlos en Azure. De forma similar a FTP, el Servicio de aplicaciones no proporciona servicios adicionales.

Las herramientas de desarrollo web más populares admiten uno o varios de estos procesos de implementación. Aunque la herramienta que elija determina los procesos de implementación que puede aprovechar, la funcionalidad de DevOps real a su disposición depende de la combinación del proceso de implementación y las herramientas específicas que elija. Por ejemplo, si ejecuta Web Deploy desde [Visual Studio con Azure SDK](#vspros), aunque no obtenga la automatización de Kudu, tendrá la restauración de paquetes y la automatización de MSBuild en Visual Studio. Azure SDK también proporciona un asistente fácil de usar que le ayuda a crear los recursos de Azure que necesita directamente en la interfaz de Visual Studio.

>[AZURE.NOTE] Estos procesos de implementación no [aprovisionan realmente los recursos de Azure](resource-group-portal) que puede necesitar la aplicación, como el Plan de servicio de aplicaciones, la aplicación del Servicio de aplicaciones y la Base de datos SQL. Sin embargo, la mayoría de los artículos de procedimientos vinculados muestra cómo aprovisionar la aplicación e implementar el código en ella de un extremo a otro. También puede encontrar opciones adicionales para el aprovisionamiento de recursos de Azure en la sección [Automatización de la implementación mediante herramientas de línea de comandos](#automate).

## <a name="ftp"></a>Implementación mediante la copia manual de archivos en Azure
Si está familiarizado con la copia manual de contenido web en proveedores de servicios de hosting web, un flujo de trabajo común para los desarrolladores de PHP, puede usar una utilidad [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) para copiar archivos, como el Explorador de Windows o [FileZilla](https://filezilla-project.org/).

La copia manual de archivos utiliza el protocolo FTP para la implementación (consulte [Información general sobre los procesos de implementación](#overview)).

Las ventajas de la copia manual de archivos son las siguientes:

- La familiaridad con las herramientas de FTP. 
- Sabe exactamente dónde van los archivos.
- Mayor seguridad con FTPS.
- Una buena solución de implementación si le gusta usar herramientas básicas para el desarrollo web (por ejemplo, desarrollar aplicaciones web mediante el Bloc de notas).

Los inconvenientes de la copia manual de archivos son los siguientes:

- Es responsable de la implementación de los archivos en los directorios correctos del Servicio de aplicaciones.
- No hay control de versiones para la reversión cuando se producen errores.
- Muchas herramientas de FTP no proporcionan la copia de solo diferencias y copian simplemente todos los archivos. Para aplicaciones de gran tamaño, esto lleva a tiempos de implementación largos, incluso en actualizaciones pequeñas.

### <a name="howtoftp"></a>Implementación mediante la copia manual de archivos en Azure
La copia de archivos en Azure implica unos pocos pasos sencillos:

1. Cree credenciales de implementación de la aplicación en el [Portal de Azure](https://portal.azure.com). Para ello, en la hoja de la aplicación web, haga clic en **Configuración** > **Credenciales de implementación**.
2. Después de haber configurado las credenciales de implementación, obtenga la información de conexión de FTP en **Configuración** > **Propiedades** y, a continuación, copie los valores de **FTP/Usuario de desarrollo**, **Nombre del host FTP** y **Nombre del host FTPS**.
3. Desde el cliente de FTP, use la información de conexión recopilada para conectarse a la aplicación.
4. Copie los archivos y su estructura de directorios correspondiente al directorio [**/site/wwwroot** de Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (o **/Data/Jobs** para WebJobs).
5. Vaya a la dirección URL de la aplicación para comprobar que la aplicación se está ejecutando correctamente. 

Para obtener más información, consulte los siguientes recursos:

* [Creación de un sitio web PHP-MySQL e implementación con FTP.](web-sites-php-mysql-deploy-use-ftp.md).
* [Uso de scripts por lotes de FTP](http://support.microsoft.com/kb/96269).

## <a name="dropbox"></a>Implementación mediante sincronización con una carpeta en la nube
Una buena alternativa a [copiar archivos manualmente](#ftp) es sincronizar archivos y carpetas con el Servicio de aplicaciones desde un servicio de almacenamiento en la nube, como OneDrive y Dropbox. En el [Portal de Azure](https://portal.azure.com), puede configurar una carpeta especial en el almacenamiento en la nube, trabajar con el código de la aplicación y el contenido en dicha carpeta y sincronizar con el Servicio de aplicaciones con solo pulsar un botón.

La sincronización con una carpeta en la nube utiliza el proceso Kudu para la implementación (consulte [Información general sobre los procesos de implementación](#overview)).

Las ventajas de sincronizar con una carpeta en la nube son las siguientes:

- Sencillez de la implementación Servicios como OneDrive y Dropbox proporcionan clientes de sincronización de escritorio, por lo que el directorio de trabajo local también es el directorio de implementación.
- Implementación con un solo clic.
- Toda la funcionalidad de Kudu está disponible (por ejemplo, control de versiones de implementación, reversión, restauración de paquetes, automatización).
- Una buena solución de implementación si le gusta usar herramientas básicas para el desarrollo web.

Los inconvenientes de sincronizar con una carpeta en la nube son los siguientes:

- No es una buena solución para un proyecto de equipo.

### <a name="howtodropbox"></a>Implementación mediante sincronización con una carpeta en la nube
 
* [Implementación en aplicaciones web desde Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Uso del [Portal de Azure](https://portal.azure.com) para configurar una implementación de Dropbox.
* [Implementación de Dropbox en aplicaciones web](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). Este vídeo le guiará en el proceso de conectar una carpeta de Dropbox a una aplicación web y muestra la rapidez con que puede configurar y ejecutar una aplicación web o mantenerla con una sencilla implementación de arrastrar y soltar.
* [Foro de Azure para Git, Mercurial y Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).

## Implementación mediante un IDE
Si ya usa [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) con un [Azure SDK](https://azure.microsoft.com/downloads/) o WebMatrix u otros conjuntos de aplicaciones de IDE como [Xcode](https://developer.apple.com/xcode/) y [Eclipse](https://www.eclipse.org), puede implementar en Azure directamente desde el IDE. Esta opción es perfecta para un desarrollador individual.

Visual Studio admite los tres procesos de implementación (FTP, Git y Web Deploy), en función de sus preferencias, mientras que otros IDE pueden implementar en el Servicio de aplicaciones si tienen integración FTP o Git (consulte [Información general sobre los procesos de implementación](#overview)).

Las ventajas de la implementación mediante un IDE son las siguientes:

- Reduce al mínimo, potencialmente, las herramientas para el ciclo de vida de la aplicación de un extremo a otro. Desarrolle, depure, realice un seguimiento e implemente la aplicación en Azure, todo ello sin salir de su IDE. 

Los inconvenientes de la implementación mediante un IDE son los siguientes:

- Mayor complejidad de las herramientas.
- Aún requiere un sistema de control de código fuente para un proyecto de equipo.

<a name="vspros"></a> Las ventajas adicionales de la implementación mediante Visual Studio con Azure SDK son las siguientes:

- Azure SDK hace de los recursos de Azure ciudadanos de primera clase en Visual Studio. Cree, elimine, edite, inicie y detenga aplicaciones, consulte la Base de datos SQL del back-end, depure en directo la aplicación de Azure y mucho más. 
- Edición en directo de archivos de código en Azure.
- Depuración en directo de aplicaciones en Azure.
- Explorador integrado de Azure.
- Implementación de solo diferencias. 

###<a name="vs"></a>Implementación directa desde Visual Studio

* [Introducción a Azure y ASP.NET](web-sites-dotnet-get-started.md). Creación e implementación de un proyecto web ASP.NET MVC simple mediante Visual Studio y Web Deploy.
* [Implementación de WebJobs de Azure con Visual Studio](websites-dotnet-deploy-webjobs.md). Configuración de proyectos de aplicación de consola para que se implementen como trabajos web.  
* [Implementación de una aplicación ASP.NET MVC 5 segura con suscripción, OAuth y base de datos SQL en aplicaciones web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Creación e implementación de un proyecto web ASP.NET MVC con una base de datos SQL mediante Visual Studio, Web Deploy y migraciones de Entity Framework Code First .
* [Información general sobre la implementación web para Visual Studio y ASP.NET](http://msdn.microsoft.com/library/dd394698.aspx). Una introducción básica a la implementación web con Visual Studio. Artículo antiguo, pero que incluye información que sigue siendo pertinente, incluida información general de las opciones para implementar una base de datos junto con la aplicación web, además de una lista de tareas adicionales de implementación que podría tener que hacer o configurar manualmente Visual Studio para que las haga. Este tema trata sobre implementación en general, no solo sobre implementación en aplicaciones web.
* [Implementación web de ASP.NET con Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Una serie de 12 tutoriales que abarca un rango más completo de tareas de implementación que otros de esta lista. Desde que se escribió el tutorial se han agregado algunas características de implementación de Azure, pero las notas agregadas posteriormente explican lo que falta.
* [Implementación de un sitio web ASP.NET en Azure en Visual Studio 2012 desde un repositorio Git directamente](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Explica cómo implementar un proyecto web de ASP.NET en Visual Studio, con el complemento Git para confirmar el código en Git y conectar Azure al repositorio Git. A partir de Visual Studio 2013, la compatibilidad con Git está integrada y no requiere la instalación de un complemento.

###<a name="webmatrix"></a>Implementación directa desde WebMatrix

* [Creación e implementación de un sitio web Node.js en Azure con WebMatrix](web-sites-nodejs-use-webmatrix.md).
* [WebMatrix 3: Git integrado e implementación en Azure.](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). Uso de WebMatrix para implementar desde un repositorio de control de código fuente de Git.

## <a name="onprem"></a>Implementación desde un sistema de control de código fuente local

Si trabaja en un equipo de desarrollo de cualquier tamaño y utiliza un sistema de administración de código fuente (SCM) local, como [Team Foundation Server](https://www.visualstudio.com/products/tfs-overview-vs.aspx) (TFS), [Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) o [Mercurial](http://mercurial.selenic.com/), puede configurar el Servicio de aplicaciones para que se integre con el repositorio e implementar directamente en el Servicio de aplicaciones en el flujo de trabajo de control de código fuente. Si utiliza TFS, también puede configurarlo para implementar de forma continua en el Servicio de aplicaciones.

TFS usa Web Deploy para implementar en el Servicio de aplicaciones, mientras que la implementación desde repositorios de Git o Mercurial utiliza Kudu (consulte [Información general sobre los procesos de implementación](#overview)).

Las ventajas de la implementación desde un sistema de control de código fuente local son las siguientes:

- Compatibilidad con la implementación desde cualquier marco de lenguaje o cliente Git o Mercurial, incluidos [Xcode](https://developer.apple.com/xcode/) y [Eclipse](https://www.eclipse.org).
- Implementación específica de ramas, se pueden implementar versiones diferentes en [ranuras](web-sites-staged-publishing) distintas.
- Es útil para los equipos de desarrollo de cualquier tamaño.

Los inconvenientes de la implementación desde un sistema de control de código fuente local son los siguientes:

- Es necesario conocer el sistema SCM elegido.
- Puede proporcionar más funcionalidades y características de las que necesite.
- Falta de soluciones preparadas para la implementación continua y la implementación específica de ramas desde herramientas de cliente de Git y Mercurial. 

Otros inconvenientes de la implementación mediante TFS son los siguientes:

- Integración continua (CI) para compilaciones, pruebas e implementación.
- Herramientas de colaboración integradas que funcionan con su editor o IDE existente.
- Compatibilidad con Git para control de versiones distribuido o control de versiones de Team Foundation (TFVC) para un control de versiones centralizado. 
- Herramientas enriquecidas para una implementación más ágil.
- Integraciones ya preparadas para [Jenkins](https://jenkins-ci.org), [Slack](https://slack.com), [ZenDesk](https://www.zendesk.com), [Trello](https://trello.com), [Bus de servicio de Azure](/services/service-bus/) y muchos más. 
- [Team Foundation Server Express](https://www.microsoft.com/download/details.aspx?id=48259) es gratuito para un equipo de hasta cinco desarrolladores.

###<a name="tfs"></a>Implementación continua mediante TFS

* [Entrega continua para servicios en la nube en Azure](../cloud-services/cloud-services-dotnet-continuous-delivery.md). Este documento es para un servicio en la nube de Azure, pero parte del contenido también es relevante para aplicaciones web.

###<a name="gitmercurial"></a>Implementación desde un repositorio de Git o Mercurial local

* [Publicación desde el control de código fuente en aplicaciones web con Git](web-sites-publish-source-control.md). Cómo usar Git para publicar directamente desde su equipo local en una aplicación web (en Azure, este método de publicación se denomina Git local). También muestra cómo habilitar la implementación continua de repositorios Git desde GitHub, CodePlex o BitBucket.
* [Publicación en aplicaciones web desde cualquier repositorio git/hg](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Blog que explica la característica "Repositorio externo" en aplicaciones web.
* [Foro de Azure para Git, Mercurial y Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).
* [Implementación de dos sitios web en Azure desde un repositorio Git](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx). Publicación en el blog de Scott Hanselman.

## Implementación desde un servicio de control de código fuente basado en la nube
Si trabaja en un equipo de desarrollo de cualquier tamaño y utiliza un servicio de administración de código fuente (SCM) en la nube como [Visual Studio Team Services](http://www.visualstudio.com/) (anteriormente, Visual Studio Online), [GitHub](https://www.github.com), [GitLab](https://gitlab.com), [BitBucket](https://bitbucket.org/), [CodePlex](https://www.codeplex.com/), [Codebase](https://www.codebasehq.com) y [Kiln](https://www.fogcreek.com/kiln/), puede configurar el Servicio de aplicaciones para que se integre con el repositorio e implementar continuamente.

Visual Studio Team Services utiliza Web Deploy para implementar en el Servicio de aplicaciones, mientras que la implementación desde repositorios en línea utiliza Kudu (consulte [Información general sobre los procesos de implementación](#overview)).

Las ventajas de la implementación desde un servicio de control de código fuente basado en la nube son las siguientes:

- Compatibilidad con cualquier lenguaje de marco de trabajo.
- Implementación continua sin configuración para repositorios Git y Mercurial. 
- Implementación específica de ramas, se pueden implementar ramas diferentes en [ranuras](web-sites-staged-publishing) distintas.
- Es útil para los equipos de desarrollo de cualquier tamaño.

Los inconvenientes de la implementación desde un servicio de control de código fuente basado en la nube son los siguientes:

- Es necesario conocer el servicio SCM elegido.
- Puede proporcionar más funcionalidades y características de las que necesite.

Las ventajas adicionales de la implementación mediante Visual Studio Team Services son las siguientes:

- Es gratuito para un equipo de hasta cinco desarrolladores.
- Integración continua (CI) para compilaciones, pruebas e implementación.
- Herramientas de colaboración integradas que funcionan con su editor o IDE existente.
- Compatibilidad con Git para control de versiones distribuido o control de versiones de Team Foundation (TFVC) para un control de versiones centralizado. 
- Herramientas enriquecidas para una implementación más ágil.
- Integraciones ya preparadas para [Jenkins](https://jenkins-ci.org), [Slack](https://slack.com), [ZenDesk](https://www.zendesk.com), [Trello](https://trello.com), [Bus de servicio de Azure](/services/service-bus/) y muchos más. 
- Paneles enriquecidos para una fácil creación de informes con [conexión a Power BI](https://www.visualstudio.com/get-started/report/report-on-vso-with-power-bi-vs).

###<a name="vsts"></a>Implementación continua con Visual Studio Team Services

- [Entrega continua a Azure mediante Visual Studio Team Services y TFVC](../cloud-services/cloud-services-continuous-delivery-use-vso.md). Tutorial paso a paso que muestra cómo configurar la entrega continua desde Visual Studio Team Services a una aplicación web, mediante TFVC. 
- [Entrega continua a Azure con Visual Studio Team Services y Git](../cloud-services/cloud-services-continuous-delivery-use-vso-git.md). Similar al tutorial anterior, pero usa Git en lugar de TFVC.

###<a name="cloudgitmercurial"></a>Implementación desde un repositorio de Git o Mercurial basado en la nube

- [Publicación desde el control de código fuente en aplicaciones web con Git](web-sites-publish-source-control.md). Habilitación de la implementación continua de repositorios desde GitHub, CodePlex o BitBucket. A pesar de que este tutorial muestra cómo publicar un repositorio Git, el proceso para los repositorios de Mercurial hospedados en CodePlex o BitBucket es similar.
- [Implementación en aplicaciones web con GitHub con Kudu](https://azure.microsoft.com/documentation/videos/deploying-to-azure-from-github/). Vídeo de Scott Hanselman y David Ebbo que muestra cómo implementar una aplicación web directamente desde GitHub en el Servicio de aplicaciones.
- [Implementación en Azure Button para aplicaciones web](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/). Blog sobre un método para desencadenar la implementación desde un repositorio Git.
- [Foro de Azure para Git, Mercurial y Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).

Para obtener más información, consulte los siguientes recursos:

- [Publicación desde el control de código fuente en aplicaciones web con Git](web-sites-publish-source-control.md). Cómo usar Git para publicar directamente desde su equipo local en aplicaciones web (en Azure, este método de publicación se denomina Git local). 

## <a name="automate"></a>Automatización de la implementación mediante herramientas de línea de comandos

* [Automatización de la implementación con MSBuild](#msbuild)
* [Copia de archivos con scripts y herramientas FTP](#ftp)
* [Automatización de la implementación con Windows PowerShell](#powershell)
* [Automatización de la implementación con la API de administración de .NET](#api)
* [Implementación desde la interfaz de la línea de comandos de Azure (CLI de Azure)](#cli)
* [Implementación desde la línea de comandos de Web Deploy](#webdeploy)
* [Uso de scripts por lotes de FTP](http://support.microsoft.com/kb/96269).
 
Otra opción de implementación es usar un servicio basado en la nube como [Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy). Para obtener más información, consulte [Deploy ASP.NET applications to Azure Web Sites](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites) (Implementación de aplicaciones ASP.NET en Sitios web de Azure).

###<a name="msbuild"></a>Automatización de la implementación con MSBuild

Si usa el [IDE de Visual Studio](#vs) para desarrollo, puede utilizar [MSBuild](http://msbuildbook.com/) para automatizar todo lo que puede hacer en el IDE. Puede configurar MSBuild para usar [Web Deploy](#webdeploy) o [FTP/FTPS](#ftp) para copiar archivos. Web Deploy también puede automatizar muchas otras tareas relacionadas con la implementación, como implementar bases de datos.

Para obtener más información acerca de la implementación de la línea de comandos con MSBuild, consulte los siguientes recursos:

* [Implementación web de ASP.NET con Visual Studio: Implementación de línea de comandos](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). El décimo tutorial de una serie sobre la implementación en Azure con Visual Studio. Muestra cómo usar la línea de comandos para la implementación después de haber configurado perfiles de publicación en Visual Studio.
* [Dentro de Microsoft Build Engine: Uso de MSBuild y Team Foundation Build](http://msbuildbook.com/). Copia impresa de un libro que incluye capítulos sobre cómo usar MSBuild para la implementación.

###<a name="powershell"></a>Automatización de la implementación con Windows PowerShell

Desde [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx) puede ejecutar funciones de implementación MSBuild o FTP. Si lo hace, también puede usar una recopilación de cmdlets de Windows PowerShell que hacen que sea fácil llamar a la API REST de administración de Azure.

Para obtener más información, consulte los siguientes recursos:

* [Implementación de una aplicación web vinculada a un repositorio de GitHub](app-service-web-arm-from-github-provision.md)
* [Aprovisionamiento de una aplicación web con una base de datos SQL](app-service-web-arm-with-sql-database-provision.md)
* [Aprovisionamiento e implementación predecibles de microservicios en Azure](app-service-deploy-complex-application-predictably.md)
* [Creación de aplicaciones reales en la nube con Azure: automatizar todo.](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Capítulo de un libro electrónico que explica cómo la aplicación de ejemplo que aparece en el libro electrónico usa scripts de Windows PowerShell para crear un entorno de prueba de Azure e implementar en él. Consulte la sección [Recursos](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) para ver vínculos a documentación adicional de Azure PowerShell.
* [Uso de scripts de Windows PowerShell para publicar en entornos de prueba y desarrollo](http://msdn.microsoft.com/library/dn642480.aspx). Cómo usar scripts de implementación de Windows PowerShell que genera Visual Studio.

###<a name="api"></a>Automatización de la implementación con la API de administración de .NET

Puede escribir código de C# para realizar funciones de MSBuild o FTP para implementación. Si lo hace, puede tener acceso a la API REST de administración de Azure para realizar funciones de administración de sitios.

Para obtener más información, consulte el siguiente recurso:

* [Automatización de todo con las bibliotecas de administración de Azure y .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Introducción a la API de administración .NET y vínculos a más documentación.

###<a name="cli"></a>Implementación desde la interfaz de la línea de comandos de Azure (CLI de Azure)

Puede usar la línea de comandos en máquinas Windows, Mac o Linux para implementar mediante FTP. Si lo hace, también puede tener acceso a la API de administración REST de Azure con la CLI de Azure.

Para obtener más información, consulte el siguiente recurso:

* [Herramientas de línea de comandos de Azure](/downloads/#cmd-line-tools). Página de portal en Azure.com para obtener información sobre la herramienta de línea de comandos.

###<a name="webdeploy"></a>Implementación desde la línea de comandos de Web Deploy

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) es el software de Microsoft para la implementación en IIS que no solo ofrece características inteligentes de sincronización de archivos, sino que también puede realizar o coordinar muchas otras tareas relacionadas con la implementación que no se pueden automatizar cuando usa FTP. Por ejemplo, Web Deploy puede implementar una base de datos nueva o actualizaciones de base de datos junto con su aplicación web. Web Deploy también puede minimizar el tiempo que se requiere para actualizar un sitio existente, dado que puede copiar de manera inteligente solo los archivos modificados. Microsoft WebMatrix, Visual Studio, Visual Studio Team Services y Team Foundation Server cuentan con compatibilidad integrada para Web Deploy, pero solo puede usar Web Deploy directamente desde la línea de comandos para automatizar la implementación. Los comandos de Web Deploy son muy poderosos, pero la curva de aprendizaje puede ser pronunciada.

Para obtener más información, consulte el siguiente recurso:

* [Aplicaciones web simples: implementación](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog de David Ebbo sobre una herramienta que escribió para facilitar el uso de Web Deploy.
* [Herramienta de implementación web](http://technet.microsoft.com/library/dd568996). Documentación oficial sobre el sitio de Microsoft TechNet. Información antigua, pero sigue siendo un buen lugar para comenzar.
* [Uso de Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Documentación oficial sobre el sitio de Microsoft IIS.NET. También es información antigua, pero es un buen lugar para comenzar.
* [StackOverflow](http://www.stackoverflow.com). El mejor sitio para visitar y obtener información más actualizada sobre el uso de Web Deploy desde la línea de comandos.
* [Implementación web de ASP.NET con Visual Studio: Implementación de línea de comandos](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild es el motor de compilación que emplea Visual Studio y que también se puede usar desde la línea de comandos para implementar aplicaciones web en Web Apps. Este tutorial forma parte de una serie que se refiere principalmente a la implementación de Visual Studio.

##<a name="nextsteps"></a>Pasos siguientes

En algunos escenarios, es posible que desee poder cambiar fácilmente entre una versión de ensayo y versión una de producción de su aplicación web. Para obtener más información, consulte [Implementación de ensayo en Web Apps](web-sites-staged-publishing.md).

Tener un plan de copia de seguridad y restauración es un elemento importante de cualquier flujo de trabajo de implementación. Para obtener información sobre la característica de copia de seguridad y restauración de Web Apps, consulte [Copias de seguridad de Web Apps](web-sites-backup.md).

Para obtener información sobre cómo usar el Control de acceso basado en roles de Azure para administrar el acceso a la implementación de Web Apps, consulte [RBAC y la publicación de aplicaciones web](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/).

Para obtener información sobre otros temas de implementación, consulte la sección Implementación en la [Documentación de aplicaciones web](/documentation/services/web-sites/).

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
 

<!---HONumber=AcomDC_0211_2016-->