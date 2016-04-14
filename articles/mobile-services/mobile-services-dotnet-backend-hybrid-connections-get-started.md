<properties
	pageTitle="Conexión a un servidor SQL Server local desde un servicio móvil back-end de .NET mediante conexiones híbridas | Servicios móviles de Azure"
	description="Más información sobre cómo conectarse a un SQL Server local desde un servicio móvil back-end de .NET mediante conexiones híbridas"
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="glenga"/>


# Conexión a un servidor SQL Server local desde Servicios móviles de Azure mediante conexiones híbridas

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


Es posible que al realizar la transición a la nube, su empresa no pueda migrar todos los recursos a Azure inmediatamente. Las conexiones híbridas permite que los Servicios móviles de Azure se conecten a los recursos locales. De este modo, puede hacer que los datos locales sean accesibles para los clientes móviles mediante el uso de Azure. Entre los activos admitidos se incluye cualquier recurso que se ejecute en un puerto TCP estático, como Microsoft SQL Server, MySQL, API Web HTTP y la mayoría de los servicios web personalizados. Las conexiones híbridas emplean la autorización de firma de acceso compartido (SAS) para proteger las conexiones del servicio móvil y el Administrador de conexiones híbridas a la conexión híbrida. Para obtener más información, consulte [Introducción a las conexiones híbridas](../integration-hybrid-connection-overview.md).

En este tutorial, aprenderá a modificar un servicio móvil back-end de .NET para usar una base de datos SQL Server local en lugar de la base de datos SQL de Azure aprovisionada con su servicio. También se admiten conexiones híbridas para un servicio móvil de back-end de JavaScript, tal y como se describe en [este artículo](http://blogs.msdn.com/b/azuremobile/archive/2014/05/12/connecting-to-an-external-database-with-node-js-backend-in-azure-mobile-services.aspx).

##Requisitos previos##

Para realizar este tutorial se requiere lo siguiente:

- **Un servicio móvil de back-end de .NET existente** <br/>Siga el tutorial [Introducción a Servicios móviles] para crear y descargar un nuevo servicio móvil de back-end de .NET desde el [Portal de Azure clásico].

[AZURE.INCLUDE [hybrid-connections-prerequisites](../../includes/hybrid-connections-prerequisites.md)]

## Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos SQL Server local

[AZURE.INCLUDE [hybrid-connections-create-on-premises-database](../../includes/hybrid-connections-create-on-premises-database.md)]

## Creación de una conexión híbrida

[AZURE.INCLUDE [hybrid-connections-create-new](../../includes/hybrid-connections-create-new.md)]

## Instalación del Administrador de conexiones híbridas local para realizar la conexión

[AZURE.INCLUDE [hybrid-connections-install-connection-manager](../../includes/hybrid-connections-install-connection-manager.md)]

## Configuración del proyecto de servicio móvil para conectarse a la base de datos de SQL Server

En este paso, defina una cadena de conexión para la base de datos local y modifique el servicio móvil para que use esta conexión.

1. En Visual Studio 2013, abra el proyecto que define el servicio móvil de back-end de .NET.

	Para aprender a descargar el proyecto de back-end de .NET, vea [Introducción a los Servicios móviles](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md).

2. En el Explorador de soluciones, abra el archivo Web.config, busque la sección **connectionStrings** y agregue una nueva entrada de SqlClient similar al siguiente, que apunta a la base de datos de SQL Server local:

	    <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

	No olvide reemplazar `<**secure_password**>` en esta cadena con la contraseña que creó para *HbyridConnectionLogin*.

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

##Prueba de la conexión de base de datos local

Antes de publicar en Azure y usar la conexión híbrida, es una buena idea asegurarse de que la conexión de base de datos funciona cuando se ejecuta localmente. De esta forma, puede diagnosticar y corregir más fácilmente los problemas de conexión antes de volver a publicar y comenzar a usar la conexión híbrida.

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]

## Actualización de Azure para usar la cadena de conexión local

Ahora que ha comprobado la conexión de base de datos, deberá agregar una configuración de la aplicación para esta nueva cadena de conexión de modo que se pueda usar desde Azure y pueda publicar el servicio móvil en Azure.

1. En el [Portal de Azure clásico], vaya al servicio móvil.

1. Haga clic en la pestaña **Configurar** y busque la sección **Cadenas de conexión**.

	![Cadena de conexión de la base de datos local](./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png)

2. Agregue una nueva cadena de conexión de **SQL Server** llamada `OnPremisesDBConnection` con un valor similar al siguiente:

		Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>


	Reemplace `<**secure_password**>` por la contraseña segura de *HybridConnectionLogin*.

2. Presione **Guardar** para guardar la conexión híbrida y la cadena de conexión que acaba de crear.

3. Con Visual Studio, publique el proyecto de servicio móvil actualizado en Azure.

	Se muestra la página de inicio del servicio.

4. Con el uso del botón **Probar ahora**, situado en la página de inicio como antes, o de una aplicación cliente conectada a su servicio móvil, invoque algunas operaciones que generen cambios en la base de datos.

	>[AZURE.NOTE]Cuando use el botón **Probar ahora** para iniciar las páginas de la API de ayuda, recuerde que debe proporcionar la clave de aplicación como contraseña (con un nombre de usuario en blanco).

4. En SQL Server Management Studio, conéctese a la instancia de SQL Server, abra el Explorador de objetos, expanda la base de datos **OnPremisesDB** y expanda **Tablas**.

5. Haga clic con el botón secundario en la tabla **hybridService1.TodoItems** y elija **Seleccionar las 1000 filas principales** para ver los resultados.

	Tenga en cuenta que el servicio móvil guarda los cambios que se generan en la aplicación en la base de datos local usando la conexión híbrida.

##Otras referencias##

+ [Sitio web de conexiones híbridas](../../services/biztalk-services/)
+ [Introducción a las conexiones híbridas](../integration-hybrid-connection-overview.md)
+ [Servicios de BizTalk: pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas](../biztalk-dashboard-monitor-scale-tabs.md)
+ [Modificación del modelo de datos de un servicio móvil back-end de .NET](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)

<!-- IMAGES -->


<!-- Links -->
[Portal de Azure clásico]: http://manage.windowsazure.com
[Introducción a Servicios móviles]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

<!---HONumber=AcomDC_0211_2016-->