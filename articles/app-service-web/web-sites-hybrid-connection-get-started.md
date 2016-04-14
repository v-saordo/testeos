<properties 
	pageTitle="Acceso a recursos locales mediante conexiones híbridas en el Servicio de aplicaciones de Azure" 
	description="Creación de una conexión entre una aplicación web en Servicio de aplicaciones de Azure y un recurso local que usa un puerto TCP estático" 
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
	ms.date="02/03/2016" 
	ms.author="cephalin"/>

#Acceso a recursos locales mediante conexiones híbridas en el Servicio de aplicaciones de Azure

Puede conectar una aplicación del Servicio de aplicaciones de Azure a cualquier recurso local que use un puerto TCP estático, como SQL Server, MySQL, API Web HTTP y la mayoría de los servicios web personalizados. En este artículo se muestra cómo crear una conexión híbrida entre el Servicio de aplicaciones y una base de datos de SQL Server local.

> [AZURE.NOTE] La parte Aplicaciones web de la característica Conexiones híbridas solo está disponible en el [Portal de Azure](https://portal.azure.com). Para crear una conexión en Servicios de BizTalk, consulte [Conexiones híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274).
> 
> Este contenido también se aplica a Aplicaciones móviles en el Servicio de aplicaciones de Azure.

## Requisitos previos
- Una suscripción de Azure. Para obtener una suscripción gratuita, consulte [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 
 
	Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

- Para usar una base de datos de SQL Server o SQL Server Express local con una conexión híbrida, es necesario habilitar TCP/IP en un puerto estático. Se recomienda usar una instancia predeterminada en SQL Server porque usa el puerto estático 1433. Para obtener información sobre cómo instalar y configurar SQL Server Express para su uso con conexiones híbridas, consulte [Conexión a un servidor SQL Server local desde un sitio web de Azure mediante conexiones híbridas](http://go.microsoft.com/fwlink/?LinkID=397979).

- El equipo en el que instala el agente local de Administrador de conexiones híbridas se describe más adelante en este artículo:

	- Debe ser capaz de conectarse a Azure a través del puerto 5671
	- Debe poder establecer comunicación con el *nombre de host*:*número de puerto* de su recurso local. 

> [AZURE.NOTE] En estos pasos de este artículo se supone que usa el explorador del equipo que hospeda el agente de conexiones híbridas local.


## Creación de una aplicación web en el Portal de Azure ##

> [AZURE.NOTE] Si ya ha creado un back-end de aplicación web o aplicación móvil en el Portal de Azure que desea usar en este tutorial, puede omitir este paso e ir directamente a [Creación de una conexión híbrida y un servicio de BizTalk](#CreateHC) y comenzar desde allí.

1. En la esquina superior izquierda del [Portal de Azure](https://portal.azure.com), haga clic en **Nuevo** > **Web y móvil** > **Aplicación web**.
	
	![New web app][NewWebsite]
	
2. En la hoja **Aplicación web**, proporcione una dirección URL y haga clic en **Crear**.
	
	![Website name][WebsiteCreationBlade]
	
3. Transcurridos unos segundos, la aplicación web se creará y aparecerá su hoja de aplicación web. El cuadro es un panel que se desplaza en vertical y que le permite administrar su sitio.
	
	![Website running][WebSiteRunningBlade]
	
4. Para comprobar que el sitio está activo, puede hacer clic en el icono **Examinar** para mostrar la página predeterminada.
	
	![Haga clic en examinar para ver su aplicación web][Browse]
	
	![Página de aplicación web predeterminada][DefaultWebSitePage]
	
A continuación, creará una conexión híbrida y un servicio de BizTalk para la aplicación web.

<a name="CreateHC"></a>
## Creación de una conexión híbrida y un servicio de BizTalk ##

1. En la hoja de la aplicación web, haga clic en **Todas las configuraciones** > **Redes** > **Configurar los puntos de conexión híbrida**.
	
	![Hybrid connections][CreateHCHCIcon]
	
2. En la hoja de conexiones híbridas, haga clic en **Agregar**.
	
	<!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
	
3. Se abre la hoja **Agregar una conexión híbrida**. Dado que esta es su primera conexión híbrida, la opción **Nueva conexión híbrida** aparece preseleccionada y se abre la hoja **Crear conexión híbrida**.
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	En la **hoja Crear conexión híbrida**:
	- En **Nombre**, escriba un nombre para la conexión.
	- En **Nombre de host**, escriba el nombre del equipo local que hospeda el recurso.
	- En **Puerto**, escriba el número de puerto que usa su recurso local (1433 para una instancia predeterminada de SQL Server).
	- Haga clic en **Servicio de BizTalk**.


4. Se abre la hoja **Crear servicio de BizTalk**. Escriba un nombre para el servicio de BizTalk y, a continuación, haga clic en **Aceptar**.
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	La hoja **Crear servicio de BizTalk** se cierre y vuelve a la hoja **Crear conexión híbrida**.
	
5. En la hoja Crear conexión híbrida, haga clic en **Aceptar**.
	
	![Haga clic en Aceptar][CreateBTScomplete]
	
6. Al finalizar el proceso, el área de notificaciones del portal le informa de que la conexión se ha creado correctamente.
	<!-- TODO

    Everything fails at this step. I can't create a BizTalk service in the dogfood portal. I switch to the classic portal
	(full portal) and created the BizTalk service but it doesn't seem to let you connnect them - When you finish the
	Create hybrid conn step, you get the following error
	Failed to create hybrid connection RelecIoudHC. The 
	resource type could not be found in the namespace 
	'Microsoft.BizTaIkServices for api version 2014-06-01'.
	
	The error indicates it couldn't find the type, not the instance.
	![Success notification][CreateHCSuccessNotification]
	-->
7. En la hoja de la aplicación web, el icono **Conexiones híbridas** ahora muestra que se creó una conexión híbrida.
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
Llegados a este punto, ha completado una parte importante de la infraestructura de conexión híbrida en la nube. A continuación, creará la parte local correspondiente.

<a name="InstallHCM"></a>
## Instalación del Administrador de conexiones híbridas local para realizar la conexión ##

1. En la hoja de la aplicación web, haga clic en **Todas las configuraciones** > **Redes** > **Configurar los puntos de conexión híbrida**. 
	
	![Hybrid connections icon][HCIcon]
	
2. En la hoja **Conexiones híbridas**, la columna **Estado** del extremo agregado recientemente muestra **No conectado**. Haga clic en la conexión para configurarla.
	
	![Not connected][NotConnected]
	
	Se abre el cuadro de la conexión híbrida.
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. En la hoja, haga clic en **Configuración del agente de escucha**.
	
	![Click Listener Setup][ClickListenerSetup]
	
4. Se abre la hoja **Propiedades de conexión híbrida**. En **Administrador de conexiones híbridas local**, elija **Haga clic aquí para instalar**.
	
	![Click here to install][ClickToInstallHCM]
	
5. En el cuadro de diálogo de advertencia de seguridad Ejecución de la aplicación, elija **Ejecutar** para continuar.
	
	![Choose Run to continue][ApplicationRunWarning]
	
6.	En el cuadro de diálogo **Control de cuentas de usuario**, elija **Sí**.
	
	![Choose Yes][UAC]
	
7. El Administrador de conexiones híbridas se descarga y se instala.
	
	![Instalación][HCMInstalling]
	
8. Cuando finalice la instalación, haga clic en **Cerrar**.
	
	![Click Close][HCMInstallComplete]
	
	En la hoja **Conexiones híbridas**, la columna **Estado** ahora muestra **Conectado**.
	
	![Connected Status][HCStatusConnected]

Ahora que la infraestructura de conexión híbrida se ha completado, puede crear una aplicación híbrida que la utilice.

>[AZURE.NOTE]En las secciones siguientes se muestra cómo usar una conexión híbrida con un proyecto de back-end .NET de Aplicaciones móviles.

## Configuración del proyecto de back-end .NET de la aplicación móvil para conectarse a la base de datos de SQL Server

En Servicio de aplicaciones, un proyecto de back-end .NET de Aplicaciones móviles es simplemente una aplicación web de ASP.NET con un SDK de Aplicaciones móviles adicional instalado e inicializado. Para usar la aplicación web como un back-end de Aplicaciones móviles, debe [descargar e inicializar el SDK del back-end .NET de Aplicaciones móviles](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#install-sdk).

Para Aplicaciones móviles, también necesita definir una cadena de conexión para la base de datos local y modificar el back-end para usar esta conexión.

1. En el Explorador de soluciones de Visual Studio, abra el archivo Web.config para el back-end .NET de la aplicación móvil, busque la sección **connectionStrings** y agregue una nueva entrada de SqlClient similar a la siguiente, que apunta a la base de datos de SQL Server local:

	    <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

	No olvide reemplazar `<**secure_password**>` en esta cadena por la contraseña que creó para *HbyridConnectionLogin*.

3. Haga clic en **Guardar** en Visual Studio para guardar el archivo Web.config.

	> [AZURE.NOTE]Esta configuración de conexión se usa cuando se ejecuta en el equipo local. Cuando se ejecuta en Azure, esta configuración se reemplaza por la configuración de conexión definida en el portal.

4. Expanda la carpeta **Modelos** y abra el archivo de modelo de datos que termina con *Context.cs*.

6. Modifique el constructor de instancia **DbContext** para pasar el valor `OnPremisesDBConnection` al constructor **DbContext** base, de forma parecida al siguiente fragmento:

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }

	El servicio usará ahora la nueva conexión a la base de datos de SQL Server.

## Actualización del back-end de la aplicación móvil para usar la cadena de conexión local

A continuación, deberá agregar una configuración de aplicación para esta cadena de conexión nueva para poder utilizarla desde Azure.

1. En el [Portal de Azure](https://portal.azure.com) en el código de back-end de aplicación web de la aplicación móvil, haga clic en **Toda la configuración** y luego en **Configuración de la aplicación**.

3. En la hoja **Configuración de aplicación web**, desplácese a **Cadenas de conexión** y agregue una nueva cadena de conexión de **SQL Server** denominada `OnPremisesDBConnection` con un valor como `Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>`.

	Sustituya `<**secure_password**>` por la contraseña segura de su base de datos local.

	![Cadena de conexión de la base de datos local](./media/web-sites-hybrid-connection-get-started/set-sql-server-database-connection.png)

2. Presione **Guardar** para guardar la conexión híbrida y la cadena de conexión que acaba de crear.

Llegados a este punto puede volver a publicar el proyecto de servidor y probar la nueva conexión con los clientes existentes de Aplicaciones móviles. Mediante la conexión híbrida, se realizarán operaciones de escritura y lectura de datos en la base de datos local.

<a name="NextSteps"></a>
## Pasos siguientes ##

- Para obtener información sobre cómo crear una aplicación web de ASP.NET que use una conexión híbrida, consulte [Conexión a un servidor SQL Server local desde un sitio web de Azure mediante conexiones híbridas](http://go.microsoft.com/fwlink/?LinkID=397979). 

### Recursos adicionales

[Introducción a las conexiones híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist presenta las conexiones híbridas (vídeo de Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Sitio web de conexiones híbridas](https://azure.microsoft.com/services/biztalk-services/)

[Servicios de BizTalk: pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas](../biztalk-dashboard-monitor-scale-tabs/)

[Creación de una nube híbrida del mundo real con una perfecta portabilidad de aplicaciones (vídeo de Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Conexión a un servidor SQL Server local desde Servicios móviles de Azure mediante conexiones híbridas (vídeo de Canal 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[New]: ./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]: ./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]: ./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]: ./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]: ./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]: ./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]: ./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]: ./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]: ./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]: ./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]: ./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]: ./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]: ./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]: ./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]: ./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]: ./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]: ./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]: ./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]: ./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]: ./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]: ./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]: ./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]: ./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
 

<!---HONumber=AcomDC_0211_2016-->