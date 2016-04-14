<properties
	pageTitle="Conexión a un servidor SQL local desde una aplicación web en el Servicio de aplicaciones de Azure mediante Conexiones híbridas"
	description="Crear una aplicación web en Microsoft Azure y conectarla a una base de datos de SQL Server local"
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="cephalin"/>

# Conexión a un servidor SQL local desde una aplicación web en el Servicio de aplicaciones de Azure mediante Conexiones híbridas

Conexiones híbridas puede conectar Aplicaciones web del [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) con recursos locales que usan puerto TCP estático. Los recursos admitidos incluyen Microsoft SQL Server, MySQL, API web HTTP, Servicios móviles y la mayoría de servicios web personalizados.

En este tutorial aprenderemos a crear un aplicación web del Servicio de aplicaciones en el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715), a conectar la aplicación web a una base de datos de SQL Server local usando la nueva característica Conexión híbrida, a crear una aplicación ASP.NET simple que usará la conexión híbrida y a implementar la aplicación en la aplicación web del Servicio de aplicaciones. La aplicación web completada en Azure almacena credenciales de usuario en una base de datos de miembros de pertenencia local. En el tutorial se asume que no tiene ninguna experiencia anterior con Azure o ASP.NET.

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de suscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
>
>La parte Aplicaciones web de la característica Conexiones híbridas solo está disponible en el [Portal de Azure](https://portal.azure.com). Para crear una conexión en Servicios de BizTalk, consulte [Conexiones híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274).

## Requisitos previos ##

Para completar este tutorial, necesitará los siguientes productos. Todos están disponibles en versión gratuita, así que puede comenzar a desarrollar en Azure completamente gratis.

- **Suscripción de Azure**: para obtener una suscripción gratuita, consulte [Prueba gratuita de Azure](/pricing/free-trial/).

- **Visual Studio 2013**: para descargar una versión de evaluación gratuita de Visual Studio 2013, consulte [Descargas de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). Instale esta aplicación antes de continuar.

- **Microsoft .NET Framework 3.5 Service Pack 1**: si su sistema operativo es Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 o Windows Server 2008 R2, puede habilitar este producto en Panel de control > Programas y características > Activar o desactivar las características de Windows. De lo contrario, puede descargarlo desde el [Centro de descargas de Microsoft](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22).

- **SQL Server 2014 Express with Tools**: descargue Microsoft SQL Server Express de forma gratuita en la [página de bases de datos de Plataforma web de Microsoft](http://www.microsoft.com/web/platform/database.aspx). Elija la versión **Express** (no LocalDB). La versión **Express with Tools** incluye SQL Server Management Studio, que usará en este tutorial.

- **SQL Server Management Studio Express**: este producto se incluye en la descarga de SQL Server 2014 Express with Tools mencionada anteriormente, pero si lo instala por separado, puede descargarlo e instalarlo desde la [página de descargas de SQL Server Express](http://www.microsoft.com/web/platform/database.aspx).

En este tutorial se supone que tiene una suscripción de Azure, que ha instalado Visual Studio 2013 y que ha instalado o habilitado .NET Framework 3.5. En el tutorial se muestra cómo instalar SQL Server 2014 Express en una configuración que funcione bien con la característica Conexiones híbridas de Azure (una instancia predeterminada con un puerto TCP estático). Antes de iniciar el tutorial, descargue SQL Server 2014 Express with Tools desde la ubicación mencionada anteriormente sino tiene SQL Server instalado.

### Notas ###
Para usar una base de datos de SQL Server o SQL Server Express local con una conexión híbrida, es necesario habilitar TCP/IP en un puerto estático. Las instancias predeterminadas de SQL Server usan el puerto estático 1433, mientras que las instancias con nombre no.

El equipo en el que instala el administrador de conexiones híbridas local:

- Debe tener conectividad de salida a Azure a través de:

Port|Porqué
---|---
80|**Obligatorio** para el puerto HTTP, para la validación de certificados y, de forma opcional, para la conectividad de datos.
443|**Opcional** para la conectividad de datos. Si la conectividad de salida para 443 no está disponible, se usa el puerto TCP 80.
5671 y 9352|**Recomendado**, pero opcional para la conectividad de datos. Tenga en cuenta que este modo obtiene mayor rendimiento. Si la conectividad de salida para estos puertos no está disponible, se usa el puerto TCP 443.
- Debe poder establecer comunicación con el *nombre de host*:*número de puerto* de su recurso local.

En estos pasos de este artículo se supone que usa el explorador del equipo que hospeda el agente de conexiones híbridas local.

Si ya tiene SQL Server instalado en una configuración y en un entorno que cumple las condiciones descritas anteriormente, puede continuar y empezar con [Creación de una base de datos de SQL Server local](#CreateSQLDB).

<a name="InstallSQL"></a>
## R: Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos SQL Server local ##

En esta sección se muestra cómo instalar SQL Server Express, habilitar TCP/IP y crear una base de datos de forma que la aplicación web funcione con el Portal de Azure.

### Instalación de SQL Server Express ###

1. Para instalar SQL Server Express, ejecute el archivo **SQLEXPRWT\_x64\_ENU.exe** o **SQLEXPR\_x86\_ENU.exe** que descargó. Aparecerá el asistente Centro de instalación de SQL Server.

	![SQL Server Install][SQLServerInstall]

2. Elija **Nueva instalación independiente de SQL Server o agregar características a una instalación existente**. Siga las instrucciones, aceptando las elecciones y configuraciones predeterminadas, hasta llegar a la página **Configuración de instancia**.

3. En la página **Configuración de instancia**, elija **Instancia predeterminada**.

	![Choose Default Instance][ChooseDefaultInstance]

	De forma predeterminada, la instancia predeterminada de SQL Server escucha solicitudes de clientes de SQL Server en el puerto estático 1433, que es el que requiere la característica Conexiones híbridas. Las instancias con nombre usan puertos dinámicos y UDP, que Conexiones híbridas no admite.

4. Acepte las configuraciones predeterminadas en la página **Configuración del servidor**.

5. En la página **Configuración del Motor de base de datos**, bajo **Modo de autenticación**, elija **Modo mixto (autenticación de SQL Server y de Windows)** y proporcione una contraseña.

	![Choose Mixed Mode][ChooseMixedMode]

	En este tutorial se usará la autenticación de SQL Server. Asegúrese de recordar la contraseña proporcionada, ya que la necesitará más tarde.

6. Recorra el resto del asistente para completar la instalación.

### Habilitar TCP/IP ###
Para habilitar TCP/IP, usará el Administrador de configuración de SQL Server, que se instaló al instalar SQL Server Express. Siga los pasos que figuran en [Habilitar el protocolo de red TCP/IP para SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx) antes de continuar.

<a name="CreateSQLDB"></a>
### Creación de una base de datos de SQL Server local ###

La aplicación web de Visual Studio requiere una base de datos de pertenencia a la que pueda acceder Azure. Para ello, se necesita una base de datos de SQL Server o SQL Server Express (no la base de datos LocalDB que usa la plantilla MVC de forma predeterminada), por lo que seguidamente creará la base de datos de pertenencia.

1. En SQL Server Management Studio, conéctese al servidor SQL Server que acaba de instalar. (Si el cuadro de diálogo **Conectar al servidor** no se abre automáticamente, vaya al **Explorador de objetos** en el panel izquierdo, haga clic en **Conectar** y, a continuación, haga clic en **Motor de la base de datos**).![Conectar al servidor][SSMSConnectToServer]

	En **Tipo de servidor**, elija **Motor de la base de datos**. En **Nombre de servidor**, puede usar **localhost** o el nombre del equipo que esté usando. Elija **Autenticación de SQL Server** y después inicie sesión con el nombre de usuario sa y la contraseña que creó anteriormente.

2. Para crear una nueva base de datos usando SQL Server Management Studio, haga clic con el botón secundario en **Base de datos** en el Explorador de objetos y, a continuación, haga clic en **Nueva base de datos**.

	![Create new database][SSMScreateNewDB]

3. En el cuadro de diálogo **Nueva base de datos**, escriba MembershipDB para el nombre de base de datos y, a continuación, haga clic en **Aceptar**.

	![Provide database name][SSMSprovideDBname]

	Tenga en cuenta que, llegados a este punto, no realiza ningún cambio en la base de datos. La aplicación web agregará más tarde la información de pertenencia automáticamente cuando se ejecute.

4. En el Explorador de objetos, si expande **Bases de datos**, verá que se ha creado la base de datos de pertenencia.

	![MembershipDB created][SSMSMembershipDBCreated]

<a name="CreateSite"></a>
## B. Creación de una aplicación web en el Portal de Azure ##

> [AZURE.NOTE] Si ya ha creado una aplicación web en el Portal de Azure que desea usar en este tutorial, puede omitir este paso e ir directamente a [Creación de una conexión híbrida y un servicio de BizTalk](#CreateHC) y continuar desde ahí.

1. En el [Portal de Azure](https://portal.azure.com), haga clic en **Nuevo** > **Web y móvil** > **Aplicación web**.

	![New button][New]

2. Configure su aplicación web y, a continuación, haga clic en **Crear**.

	![Website name][WebsiteCreationBlade]

3. Transcurridos unos segundos, la aplicación web se creará y aparecerá su hoja de aplicación web. La hoja es un panel que se desplaza en vertical y que le permite administrar su aplicación web.

	![Website running][WebSiteRunningBlade]

	Para comprobar que la aplicación web está activa, puede hacer clic en el icono **Examinar** para mostrar la página predeterminada.

A continuación, creará una conexión híbrida y un servicio de BizTalk para la aplicación web.

<a name="CreateHC"></a>
## C. Creación de una conexión híbrida y un servicio de BizTalk ##

1. En el portal, vaya a configuración y haga clic en **Redes** > **Configurar los puntos de conexión híbrida**.

	![Hybrid connections][CreateHCHCIcon]

2. En la hoja de conexiones híbridas, haga clic en **Agregar** > **Nueva conexión híbrida**.

3. En la hoja **Crear conexión híbrida**:
	- En **Nombre**, proporcione un nombre para la conexión.
	- En **Nombre de host**, escriba el nombre del equipo del equipo host de SQL Server.
	- En **Puerto**, escriba 1433 (el puerto predeterminado para SQL Server).
	- Haga clic en **Servicio de BizTalk** > **Nuevo servicio de BizTalk** y escriba un nombre para el servicio de BizTalk.

	![Create a hybrid connection][TwinCreateHCBlades]

5. Haga clic en **Aceptar** dos veces.

	Al finalizar el proceso, el área **Notificaciones** mostrará una notificación de **ÉXITO** parpadeante de color verde y la hoja **Conexión híbrida** mostrará la conexión híbrida nueva con el estado **No conectado**.

	![One hybrid connection created][CreateHCOneConnectionCreated]

Llegados a este punto, ha completado una parte importante de la infraestructura de conexión híbrida en la nube. A continuación, creará la parte local correspondiente.

<a name="InstallHCM"></a>
## D. Instalación del Administrador de conexiones híbridas local para realizar la conexión ##

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

Ahora que la infraestructura de la conexión híbrida se ha completado, creará una aplicación web que la use.

<a name="CreateASPNET"></a>
## E. Creación de un proyecto web ASP.NET básico, edición de la cadena de conexión de base de datos y ejecución del proyecto localmente ##

### Creación de un proyecto ASP.NET básico ###
1. En Visual Studio, en el menú **Archivo**, cree un proyecto nuevo:

	![New Visual Studio project][HCVSNewProject]

2. En la sección **Plantillas** del cuadro de diálogo **Nuevo proyecto**, seleccione **Web** y elija **Aplicación web de ASP.NET** y, a continuación, haga clic en **Aceptar**.

	![Choose ASP.NET Web Application][HCVSChooseASPNET]

3. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, elija **MVC** y, a continuación, haga clic en **Aceptar**.

	![Choose MVC][HCVSChooseMVC]

4. Cuando el proyecto se haya creado, aparecerá la página Léame de la aplicación. No ejecute el proyecto web todavía.

	![Readme page][HCVSReadmePage]

### Edición de la cadena de conexión de la base de datos para la aplicación ###

En este paso editará la cadena de conexión que indica a la aplicación dónde buscar la base de datos de SQL Server Express local. La cadena de conexión es un archivo Web.config de la aplicación que contiene información de configuración para dicha aplicación.

> [AZURE.NOTE] Para garantizar que la aplicación usa la base de datos creada en SQL Server Express y no la base de datos LocalDB predeterminada de Visual Studio, es importante que complete este paso antes de ejecutar el proyecto.

1. En el Explorador de soluciones, haga doble clic en el archivo Web.config.

	![Web.config][HCVSChooseWebConfig]

2. Edite la sección **connectionStrings** para apuntar a la base de datos de SQL Server en la máquina local, siguiendo la sintaxis del ejemplo que se muestra continuación:

	![Cadena de conexión][HCVSConnectionString]

	Cuando escriba la cadena de conexión, tenga en cuenta lo siguiente:

	- Si se está conectando a una instancia con nombre en lugar de a una instancia predeterminada (por ejemplo SuServidor\\SQLEXPRESS), debe configurar su servidor SQL Server para usar puertos estáticos. Para obtener información sobre la configuración de puertos estáticos, consulte [Cómo configurar SQL Server para que escuche en un puerto específico](http://support.microsoft.com/kb/823938). De forma predeterminada, las instancias con nombre usan puertos dinámicos y UDP, que Conexiones híbridas no admite.

	- Es recomendable que especifique el puerto (1433 de forma predeterminada, como se muestra en el ejemplo) en la cadena de conexión de forma que pueda asegurarse de que su servidor SQL Server local tiene la funcionalidad TCP habilitada y usa el puerto correcto.

	- Recuerde usar Autenticación de SQL Server para conectarse, especificando el identificador y la contraseña del usuario en la cadena de conexión.

3. Haga clic en **Guardar** en Visual Studio para guardar el archivo Web.config.

### Ejecución del proyecto localmente y registro de un nuevo usuario ###

1. Ahora, ejecute el nuevo proyecto web localmente haciendo clic en el botón Examinar que se encuentra debajo de Depurar. En este ejemplo se usa Internet Explorer.

	![Run project][HCVSRunProject]

2. En la esquina superior derecha de la página web predeterminada, elija **Registrar** para registrar una cuenta nueva:

	![Register a new account][HCVSRegisterLocally]

3. Escriba un nombre de usuario y una contraseña:

	![Enter user name and password][HCVSCreateNewAccount]

	Esta operación creará automáticamente una base de datos en su servidor SQL Server local que hospedará la información de pertenencia de la aplicación. Una de las tablas (**dbo.AspNetUsers**) contiene las credenciales de usuario de la aplicación web como las que acaba de introducir. Verá esta tabla posteriormente en el tutorial.

4. Cierre la ventana del explorador de la página web predeterminada. Esta operación detendrá la aplicación en Visual Studio.

Ahora está preparado para continuar con el paso siguiente, que consiste en publicar la aplicación en Azure y probarla.

<a name="PubNTest"></a>
## F. Publicación de la aplicación web en Azure y prueba de la misma ##

Ahora publicará la aplicación en su aplicación web del Servicio de aplicaciones y después la probará para ver cómo se usa la conexión híbrida que configuró anteriormente para conectar la aplicación web a la base de datos en la máquina local.

### Publicación de la aplicación ###

1. Puede descargar el perfil de publicación para la aplicación web del Servicio de aplicaciones en el Portal de Azure. En la hoja correspondiente a su aplicación web, haga clic en **Obtener perfil de publicación** y guarde el archivo en su equipo.

	![Descargar archivo de publicación][PortalDownloadPublishProfile]

	A continuación, importará este archivo en la aplicación web de Visual Studio.

2. En Visual Studio, haga clic con el botón secundario en el nombre del proyecto en el Explorador de soluciones y seleccione **Publicar**.

	![Select publish][HCVSRightClickProjectSelectPublish]

3. En el cuadro de diálogo **Publicación web**, en la pestaña **Perfil**, elija **Importar**.

	![Importación][HCVSPublishWebDialogImport]

4. Examine el perfil de publicación descargado, selecciónelo y, a continuación, haga clic en **Aceptar**.

	![Browse to profile][HCVSBrowseToImportPubProfile]

5. Su información de publicación se importará y mostrará en la pestaña **Conexión** del cuadro de diálogo.

	![Haga clic en Publicar.][HCVSClickPublish]

	Haga clic en **Publicar**.

	Cuando la publicación se complete, el explorador se iniciará y mostrará su, ahora, aplicación de ASP.NET, ¡con la diferencia de que ahora está activa en la nube de Azure!

A continuación, usará la aplicación web activa para ver su conexión híbrida en acción.

### Prueba de la aplicación web completada en Azure ###

1. En la parte superior de la página web en Azure, elija **Iniciar sesión**.

	![Test log in][HCTestLogIn]

2. La aplicación web del Servicio de aplicaciones ahora estará conectada a la base de datos de pertenencia de la aplicación web en la máquina local. Para comprobarlo, inicie sesión con las mismas credenciales que introdujo en la base de datos local anteriormente.

	![Hello greeting][HCTestHelloContoso]

3. Para seguir probando su nueva conexión híbrida, cierre la sesión de la aplicación web de Azure y regístrese como otro usuario. Proporcione un nuevo nombre de usuario y contraseña y, a continuación, haga clic en **Registrarse**.

	![Test register another user][HCTestRegisterRelecloud]

4. Para comprobar que las credenciales del nuevo usuario se han almacenado en la base de datos local a través de la conexión híbrida, abra SQL Management Studio en el equipo local. En el Explorador de objetos, expanda la base de datos **MembershipDB** y, a continuación, expanda **Tablas**. Haga clic con el botón secundario en la tabla de pertenencia **dbo.AspNetUsers** y elija **Seleccionar las primeras 1000 filas** para ver los resultados.

	![View the results][HCTestSSMSTree]

5. La tabla de pertenencia local ahora muestra ambas cuentas, la que creó localmente y la que creó en la nube de Azure. La que creó en la nube se ha guardado en la base de datos local a través de la característica Conexión híbrida de Azure.

	![Registered users in on-premises database][HCTestShowMemberDb]

Ya ha creado e implementado una aplicación web ASP.NET que usa una conexión híbrida entre una aplicación web y la nube de Azure y una base de datos de SQL Server local. ¡Enhorabuena!

## Otras referencias ##
[Introducción a las conexiones híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist presenta las conexiones híbridas (vídeo de Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Introducción a las conexiones híbridas](/services/biztalk-services/)

[Servicios de BizTalk: pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas](../biztalk-dashboard-monitor-scale-tabs/)

[Creación de una nube híbrida del mundo real con una perfecta portabilidad de aplicaciones (vídeo de Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Conexión a un servidor SQL Server local desde un servicio móvil de Azure mediante Conexiones híbridas](../mobile-services/mobile-services-dotnet-backend-hybrid-connections-get-started.md)

[Conexión a un servidor SQL Server local desde Servicios móviles de Azure mediante conexiones híbridas (vídeo de Canal 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[Introducción a la identidad de ASP.NET](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]


<!-- IMAGES -->
[SQLServerInstall]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
[ChooseDefaultInstance]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
[ChooseMixedMode]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
[SSMSConnectToServer]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
[SSMScreateNewDB]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
[SSMSprovideDBname]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
[SSMSMembershipDBCreated]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
[New]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
[NewWebsite]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
[WebsiteCreationBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
[Browse]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
[DefaultWebSitePage]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
[CreateHCHCIcon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
[CreateHCAddHC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
[TwinCreateHCBlades]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
[CreateBTScomplete]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
[CreateHCSuccessNotification]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
[HCIcon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
[NotConnected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
[NotConnectedBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
[ClickListenerSetup]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
[ClickToInstallHCM]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
[ApplicationRunWarning]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
[UAC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
[HCMInstalling]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
[HCMInstallComplete]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
[HCStatusConnected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
[HCVSNewProject]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
[HCVSChooseASPNET]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
[HCVSChooseMVC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
[HCVSReadmePage]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
[HCVSChooseWebConfig]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
[HCVSConnectionString]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
[HCVSRunProject]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
[HCVSRegisterLocally]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
[HCVSCreateNewAccount]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
[PortalDownloadPublishProfile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
[HCVSPublishProfileInDownloadsFolder]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
[HCVSRightClickProjectSelectPublish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
[HCVSPublishWebDialogImport]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
[HCVSBrowseToImportPubProfile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
[HCVSClickPublish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
[HCTestLogIn]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
[HCTestHelloContoso]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
[HCTestRegisterRelecloud]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
[HCTestSSMSTree]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
[HCTestShowMemberDb]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png

<!---HONumber=AcomDC_0211_2016-->