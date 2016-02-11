<properties
	pageTitle="Conexión de la aplicación móvil de Azure a un servidor SQL Server local mediante conexiones híbridas | Microsoft Azure"
	description="Aprenda a conectarse a un servidor SQL Server local desde una aplicación móvil del Servicio de aplicaciones mediante conexiones híbridas"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.date="10/23/2015"
	ms.author="glenga"/>


# Conexión a un servidor SQL Server local desde aplicaciones móviles mediante conexiones híbridas

Es posible que al realizar la transición a la nube, su empresa no pueda migrar todos los recursos a Azure inmediatamente. Conexiones híbridas permite que la característica de Aplicaciones móviles en el Servicio de aplicaciones de Azure se conecte de manera segura a los recursos locales. De este modo, puede hacer que los datos locales sean accesibles para los clientes móviles mediante el uso de Azure. Entre los activos admitidos se incluye cualquier recurso que se ejecute en un puerto TCP estático, como Microsoft SQL Server, MySQL, API Web HTTP y la mayoría de los servicios web personalizados. Las conexiones híbridas emplean la autorización de firma de acceso compartido (SAS) para proteger las conexiones del servicio móvil y el Administrador de conexiones híbridas a la conexión híbrida. Para obtener más información, consulte [Introducción a las conexiones híbridas](../integration-hybrid-connection-overview.md).

En este tutorial, aprenderá a modificar un back-end de aplicación móvil .NET para usar una base de datos SQL Server local en lugar de la base de datos SQL de Azure predeterminada aprovisionada con su servicio.

[AZURE.INCLUDE [app-service-mobile-to-web-and-api](../../includes/app-service-mobile-to-web-and-api.md)]

## Requisitos previos ##

Para realizar este tutorial se requiere lo siguiente:

- **Un back-end de aplicación móvil existente** <br/>Siga el [tutorial de inicio rápido](app-service-mobile-windows-store-dotnet-get-started.md) para crear y descargar una nueva aplicación móvil de back-end .NET desde el [Portal de Azure].

[AZURE.INCLUDE [hybrid-connections-prerequisites](../../includes/hybrid-connections-prerequisites.md)]

## Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos SQL Server local

[AZURE.INCLUDE [hybrid-connections-create-on-premises-database](../../includes/hybrid-connections-create-on-premises-database.md)]

## Creación de una conexión híbrida

Deberá crear una nueva conexión híbrida y un servicio de BizTalk para la parte de código del back-end de aplicación móvil, que es una aplicación web.

1. En el [Portal de Azure], busque la aplicación móvil y haga clic en **Redes** en la configuración.

	![Ir a la aplicación web](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/mobile-app-link-to-web-app-backend.png)

	

2. Haga clic en **Configurar los puntos de la conexión híbrida** en la sección **Conexiones híbridas**.

	![Hybrid connections](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/start-hybrid-connection.png)

2. En la hoja de conexiones híbridas, haga clic en **Agregar** > **Nueva conexión híbrida**.

3. En la hoja **Crear conexión híbrida**, proporcione un **Nombre** y un **Nombre de host** para su conexión híbrida y establezca el **Puerto** en `1433`.

	![Create a hybrid connection](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/create-hybrid-connection.png)

4. Haga clic en **Servicio de Biz Talk** y escriba un nombre para el servicio de BizTalk. Haga clic en **Aceptar** dos veces.

	Este tutorial usa **mobile1**. Deberá especificar un nombre exclusivo para su nuevo servicio de BizTalk.

	Al finalizar el proceso, el área **Notificaciones** mostrará una notificación de **ÉXITO** parpadeante de color verde y la hoja **Conexión híbrida** mostrará la conexión híbrida nueva con el estado **No conectado**.

	![One hybrid connection created](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/hybrid-connection-created.png)

Llegados a este punto, ha completado una parte importante de la infraestructura de conexión híbrida en la nube. A continuación, creará una parte local correspondiente.

## Instalación del Administrador de conexiones híbridas local para realizar la conexión

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

## Configuración del proyecto de back-end de la aplicación móvil para conectarse a la base de datos de SQL Server

En este paso, defina una cadena de conexión para la base de datos local y modifique el back-end de aplicación móvil para usar esta conexión.

1. En Visual Studio 2013, abra el proyecto que define el back-end de aplicación móvil.

	Para aprender a descargar el proyecto de back-end de .NET, vea el [tutorial de inicio rápido.](app-service-mobile-windows-store-dotnet-get-started.md)

2. En el Explorador de soluciones, abra el archivo Web.config, busque la sección **connectionStrings** y agregue una nueva entrada de SqlClient similar al siguiente, que apunta a la base de datos de SQL Server local:

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

## Actualización de Azure para usar la cadena de conexión local

A continuación, deberá agregar una configuración de aplicación para esta cadena de conexión nueva para poder utilizarla desde Azure.

1. En el [Portal de Azure] en el código de back-end de aplicación web de la aplicación móvil, haga clic en **Toda la configuración** y luego en **Configuración de la aplicación**.

3. En la hoja **Configuración de aplicación web**, desplácese a **Cadenas de conexión** y agregue una nueva cadena de conexión de **SQL Server** denominada `OnPremisesDBConnection` con un valor como `Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>`.

	Sustituya `<**secure_password**>` por la contraseña segura de su base de datos local.

	![Cadena de conexión de la base de datos local](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/set-sql-server-database-connection.png)

2. Presione **Guardar** para guardar la conexión híbrida y la cadena de conexión que acaba de crear.

## Publicación y prueba del back-end de aplicación móvil en Azure

Por último, deberá publicar el back-end de aplicación móvil en Azure y comprobar que usa la conexión híbrida para almacenar los datos en la base de datos local.

3. En Visual Studio, haga clic con el botón derecho en el proyecto, haga clic en **Publicar**, luego en **Publicación web** y finalmente en **Sitios web Microsoft**.

	En lugar de usar Visual Studio, también puede [usar Git para publicar el back-end](mobile-services-dotnet-backend-store-code-source-control.md).

2. Inicie sesión con las credenciales de Azure y seleccione su servicio en **Seleccionar sitios web existentes**.

	Visual Studio descarga la configuración de publicación directamente desde Azure.

3. Finalmente, haga clic en **Publicar**.

	Una vez completada la publicación, el servicio se reinicia y se muestra la página de inicio de back-end.

4. Mediante una aplicación cliente conectada a la aplicación móvil, invoque algunas operaciones que generen cambios en la base de datos.

5. En SQL Server Management Studio, conéctese a la instancia de SQL Server, abra el Explorador de objetos, expanda la base de datos **OnPremisesDB** y expanda **Tablas**.

6. Haga clic con el botón secundario en la tabla **hybridService1.TodoItems** y elija **Seleccionar las 1000 filas principales** para ver los resultados.

	Tenga en cuenta que el back-end de aplicación móvil ha guardado en la base de datos local los cambios que se generan en la aplicación cliente mediante la conexión híbrida.

## Otras referencias ##

+ [Sitio web de conexiones híbridas](../../services/biztalk-services/)
+ [Introducción a las conexiones híbridas](../integration-hybrid-connection-overview.md)
+ [Servicios de BizTalk: pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas](../biztalk-dashboard-monitor-scale-tabs.md)
+ [Modificación del modelo de datos de un servicio móvil back-end de .NET](../mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)

<!-- IMAGES -->


<!-- Links -->
[Portal de Azure]: https://portal.azure.com/
[Azure classic portal]: http://go.microsoft.com/fwlink/p/?linkid=213885
[Get started with Mobile Services]: ../mobile-services-windows-store-dotnet-get-started.md

<!---HONumber=AcomDC_1203_2015-->