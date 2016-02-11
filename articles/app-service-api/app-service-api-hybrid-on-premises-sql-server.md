<properties 
	pageTitle="Conexión a un servidor SQL local desde una aplicación de API en el Servicio de aplicaciones de Azure mediante Conexiones híbridas" 
	description="Crear una aplicación de API en Microsoft Azure y conectarla a una base de datos de SQL Server local"
	services="app-service\api" 
	documentationCenter="" 
	authors="TomArcher" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Conexión a un servidor SQL local desde una aplicación de API en el Servicio de aplicaciones de Azure mediante Conexiones híbridas

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

Las conexiones híbridas puede conectar Aplicaciones de API del [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) con recursos locales que usan un puerto TCP estático. Los recursos admitidos incluyen Microsoft SQL Server, MySQL, API web HTTP, Servicios móviles y la mayoría de servicios web personalizados.

En este tutorial, aprenderá a crear una aplicación de API del Servicio de aplicaciones en la [vista previa de Azure](http://go.microsoft.com/fwlink/?LinkId=529715) que se conecta a una base de datos SQL Server local mediante la nueva característica de conexión híbrida. En el tutorial se asume que no tiene ninguna experiencia anterior con Azure o SQL Server.

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Requisitos previos

Para completar este tutorial, necesitará los siguientes productos. Todos están disponibles en versión gratuita, así que puede comenzar a desarrollar soluciones de Azure completamente gratis.

- **Suscripción de Azure**: para obtener una suscripción gratuita, consulte [Prueba gratuita de Azure](/pricing/free-trial/). 

- **Visual Studio**: para descargar una versión de evaluación gratuita de Visual Studio 2013 o Visual Studio 2015, vea [Descargas de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). Instale una de ellas antes de continuar. (Las capturas de pantalla de este tutorial se han obtenido con Visual Studio 2013)

- **SQL Server 2014 Express with Tools**: descargue Microsoft SQL Server Express de forma gratuita en la [página de bases de datos de Plataforma web de Microsoft](https://www.microsoft.com/es-ES/download/details.aspx?id=42299). Más adelante en este tutorial, verá cómo [instalar SQL Server](#InstallSQLDB) para asegurarse de que está correctamente configurado.

- **SQL Server Management Studio Express**: este producto se incluye en la descarga de SQL Server 2014 Express with Tools mencionada anteriormente, pero si lo instala por separado, puede descargarlo e instalarlo desde la [página de descargas de SQL Server Express](https://www.microsoft.com/es-ES/download/details.aspx?id=42299).

En este tutorial se supone que tiene una suscripción de Azure, que ha instalado Visual Studio 2013 y que ha instalado o habilitado .NET Framework 3.5. En el tutorial se muestra cómo instalar SQL Server 2014 Express en una configuración que funcione bien con la característica Conexiones híbridas de Azure (una instancia predeterminada con un puerto TCP estático). Antes de iniciar el tutorial, descargue (pero no instale) SQL Server 2014 Express with Tools desde la ubicación mencionada anteriormente sino tiene SQL Server instalado.

### Notas
Para usar una base de datos de SQL Server o SQL Server Express local con una conexión híbrida, es necesario habilitar TCP/IP en un puerto estático. Las instancias predeterminadas de SQL Server usan el puerto estático 1433, mientras que las instancias con nombre no.

El equipo en el que instala el administrador de conexiones híbridas local:

- Debe tener conectividad de salida a Azure a través de:

> <table border="1">
    <tr>
       <th><strong>Puerto</strong></th>
        <th>Porqué</th>
    </tr>
    <tr>
        <td>80</td>
        <td><strong>Obligatorio</strong> para el puerto HTTP, para la validación de certificados y, de forma opcional, para la conectividad de datos.</td>
    </tr>
    <tr>
        <td>443</td>
        <td><strong>Opcional</strong> para la conectividad de datos. Si la conectividad de salida para 443 no está disponible, se usa el puerto TCP 80.</td>
    </tr>
	<tr>
        <td>5671 y 9352</td>
        <td><strong>Recomendado</strong>, pero opcional para la conectividad de datos. Tenga en cuenta que este modo obtiene mayor rendimiento. Si la conectividad de salida para estos puertos no está disponible, se usa el puerto TCP 443.</td>
	</tr>
</table>

- Debe poder establecer comunicación con el *nombre de host*:*número de puerto* de su recurso local. 

En estos pasos de este artículo se supone que usa el explorador del equipo que hospeda el agente de conexiones híbridas local.

Si ya tiene SQL Server instalado en una configuración y en un entorno que cumple las condiciones descritas anteriormente, puede continuar y empezar con [Creación de una base de datos de SQL Server local](#CreateSQLDB).

<a name="InstallSQL"></a>
## Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos SQL Server local

En esta sección se muestra cómo instalar SQL Server Express, habilitar TCP/IP y crear una base de datos de forma que la aplicación de API funcione con el [portal de vista previa de Azure](https://portal.azure.com/).

<a name="InstallSQLDB"></a>
### Instalación de SQL Server Express

1. Para instalar SQL Server Express, descargue el archivo **SQLEXPRWT\_x64\_ENU.exe** (versión de 64 bits) o **SQLEXPR\_x86\_ENU.exe** (versión de 32 bits) y extráigalo en la carpeta deseada. 

2. Una vez extraídos los archivos de instalación de SQL Server Express, ejecute **setup.exe**.

3. Cuando aparezca **Centro de instalación de SQL Server**, elija **Nueva instalación independiente de SQL Server o agregar características a una instalación existente**.

	![Centro de instalación de SQL Server](./media/app-service-api-hybrid-on-premises-sql-server/sql-server-installation-center.png)

4. Siga las instrucciones, aceptando las elecciones y configuraciones predeterminadas, hasta llegar a la página **Configuración de instancia**.
	
5. En la página **Configuración de instancia**, elija **Instancia predeterminada** y haga clic en **Siguiente**.
	
	![Configuración de instancia](./media/app-service-api-hybrid-on-premises-sql-server/instance-configuration.png)
	
	La instancia predeterminada de SQL Server escucha solicitudes de clientes de SQL Server en el puerto estático 1433, que es el que requiere la característica Conexiones híbridas. Las instancias con nombre usan puertos dinámicos y UDP, que Conexiones híbridas no admite.
	
6. Acepte las configuraciones predeterminadas en la página **Configuración del servidor** y haga clic en **Siguiente**.
	
7. En la página **Configuración del Motor de base de datos**, bajo **Modo de autenticación**, elija **Modo mixto (autenticación de SQL Server y de Windows)** y proporcione una contraseña.
	
	![Configuración del motor de base de datos](./media/app-service-api-hybrid-on-premises-sql-server/database-engine-configuration.png)
	
	En este tutorial se usará la autenticación de SQL Server. Asegúrese de recordar la contraseña proporcionada, ya que la necesitará más tarde.
	
8. Recorra el resto del asistente para completar la instalación, y acepte los valores predeterminados a medida que avanza.

9. Cierre el cuadro de diálogo **Centro de instalación de SQL Server**.

### Habilitar TCP/IP
Para habilitar TCP/IP, usará el Administrador de configuración de SQL Server, que se instaló al instalar SQL Server Express. Siga los pasos que figuran en [Habilitar el protocolo de red TCP/IP para SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx) antes de continuar.

<a name="CreateSQLDB"></a>
### Creación de una base de datos de SQL Server local

1. En **SQL Server Management Studio**, conéctese al servidor SQL Server que acaba de instalar. En **Tipo de servidor**, elija **Motor de la base de datos**. En **Nombre de servidor**, puede usar **localhost** o el nombre del equipo que esté usando. Elija **Autenticación de SQL Server** y después inicie sesión con el nombre de usuario `sa` y la contraseña que creó anteriormente.

	![Conectar al servidor](./media/app-service-api-hybrid-on-premises-sql-server/connect-to-server.png)
	
	Si el cuadro de diálogo **Conectar al servidor** no se abre automáticamente, vaya al **Explorador de objetos** en el panel izquierdo, haga clic en **Conectar** y luego haga clic en **Motor de la base de datos**.
	
2. Para crear una nueva base de datos usando SQL Server Management Studio, haga clic con el botón derecho en **Base de datos** en el Explorador de objetos y, a continuación, haga clic en **Nueva base de datos**.
	
	![Menú Crear una base de datos nueva](./media/app-service-api-hybrid-on-premises-sql-server/new-database-menu.png)
	
3. En el cuadro de diálogo **Nueva base de datos**, escriba `LocalDatabase` como el nombre de base de datos y después haga clic en **Aceptar**.
	
	![Creación de una base de datos nueva](./media/app-service-api-hybrid-on-premises-sql-server/new-database.png)
	
### Crear y rellenar una tabla de SQL Server

1. En el **Explorador de objetos** de **SQL Server Management Studio**, expanda la entrada `LocalDatabase`.

	![Base de datos expandida](./media/app-service-api-hybrid-on-premises-sql-server/local-database-expanded.png)

2. Haga clic con el botón derecho en **Tablas** y seleccione la opción **Tabla...** en el menú contextual.

	![Menú Nueva tabla](./media/app-service-api-hybrid-on-premises-sql-server/new-table-menu.png)

3. Cuando aparezca la cuadrícula de diseño de tabla, escriba la información de la columna tal como se muestra en la ilustración siguiente.

	![Columnas de tabla nueva](./media/app-service-api-hybrid-on-premises-sql-server/table-def.png)

4. Presione **&lt;Ctrl>S** para guardar la definición de la nueva tabla. Se le pedirá que escriba un nombre de tabla. Escriba `Speakers`y presione **Aceptar**.

	![Guardar nueva tabla](./media/app-service-api-hybrid-on-premises-sql-server/save-new-table.png)

5. En el **Explorador de objetos**, haga clic con el botón derecho en la tabla `Speakers` recién creada y después seleccione **Editar las primeras 200 filas** en el menú contextual.

	![Editar las primeras 200 filas](./media/app-service-api-hybrid-on-premises-sql-server/edit-top-200-rows.png)

6. Cuando aparezca la cuadrícula para escribir o modificar los datos de la tabla, escriba algunos datos de prueba como se indica en la siguiente ilustración.

	![Datos de prueba](./media/app-service-api-hybrid-on-premises-sql-server/speakers-data.png)

## Crear e implementar la aplicación de API de demostración en Azure 

En esta sección se ofrece información detallada sobre cómo crear la aplicación de API de demostración.

1. Abra Visual Studio 2013 y seleccione **Archivo > Nuevo > Proyecto**. 

2. Seleccione la plantilla **Visual C# > Web > Aplicación web ASP.NET**, desactive la opción **Agregar Application Insights al proyecto**, asigne al proyecto el nombre *SpeakersList* y después haga clic en **Aceptar**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project.png)

3. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, seleccione la plantilla de proyecto **Aplicación de API de Azure** y después haga clic en **Aceptar**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project-api-app.png)

4. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta **Modelos** y después seleccione la opción del menú contextual **Agregar > Clase...**

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-menu.png)

5. Asigne el nombre *Speaker.cs* al nuevo archivo y haga clic en **Agregar**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-class.png)

6. Sustituya todo el contenido del archivo `Speaker.cs` por el código siguiente.

		namespace SpeakersList.Models
		{
			public class Speaker
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

7. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta **Controladores** y después seleccione la opción del menú contextual **Agregar > Controlador...**

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-controller.png)

8. En el cuadro de diálogo **Agregar scaffold**, seleccione la opción **Controlador de Web API 2 - Vacío** y haga clic en **Agregar**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-scaffold.png)

9. Asigne el nombre **SpeakersController** al controlador y haga clic en **Agregar**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-controller-name.png)

10. Reemplace el código de este archivo `SpeakersController.cs` por el código siguiente. Asegúrese de especificar sus propios valores para los marcadores de posición &lt;serverName> y &lt;password> en `connectionString`. El valor &lt;serverName> es el nombre de máquina en que se encuentra SQL Server, y el valor &lt;password> es el que se establece al instalar y configurar SQL Server.

	> [AZURE.NOTE] El siguiente fragmento de código incluye información de contraseña. Esto se hace para simplificar la demostración. En un entorno de producción real, no debería almacenar las credenciales en el código. En su lugar, consulte las [Prácticas recomendadas para implementar las contraseñas y otros datos confidenciales en ASP.NET y Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		using SpeakersList.Models;
		using System.Data.Sql;
		using System.Data.SqlClient;
		
		namespace SpeakersList.Controllers
		{
		    public class SpeakersController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Speaker> Get()
		        {
					// Instantiate List object that will be populated and returned
					// from this method.
		            List<Speaker> speakers = new List<Speaker>();
	
					// Build database connection string.
		            string connectionString = "Server=<serverName>;" +
		                                      "Initial Catalog=LocalDatabase;" +
		                                      "User Id=sa;Password=<password>;" +
		                                      "Asynchronous Processing=true;";
		
					// Build query string to select all three columns from the Speakers table.
		            string queryString = "SELECT Id, EmailAddress, Name FROM dbo.Speakers;";

					// Instantiate the SqlConnection object using the connection string.
		            using (SqlConnection connection = new SqlConnection(connectionString))
		            {
						// Instantiate the SqlCommand object using the connection and query strings.
		                SqlCommand command = new SqlCommand(queryString, connection);

						// Open the connection to the database.
		                connection.Open();

						// Execute the command and return a SqlDataReader object
						// that can be iterated.
		                SqlDataReader reader = command.ExecuteReader();
		                try
		                {
							// Read a row from the SqlDataReader object
		                    while (reader.Read())
		                    {
								// For each row, use the returned data to instantiate
								// and add to the List a new Speaker object.
		                        speakers.Add(new Speaker 
		                            { 
		                                Id = Convert.ToInt32(reader[0]), 
		                                EmailAddress = reader[1].ToString(), 
		                                Name = reader[2].ToString() 
		                            }
		                        );
		                    }
		                }
		                finally
		                {
							// Close the SqlDataReader object.
		                    reader.Close();
		                }
		            }
					// Return the List of Speaker objects.
		            return speakers;
		        }
		    }
		}

### Habilitación de la IU de Swagger

Al habilitar la interfaz de usuario de Swagger, podrá probar fácilmente sus aplicaciones de API sin tener que escribir código de cliente para llamar.

1. Abra el archivo *App\_Start/SwaggerConfig.cs* y busque **EnableSwaggerUI**:

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-disabled.png)

2. Quite la marca de comentario de las líneas de código siguientes:

	        })
	    .EnableSwaggerUi(c =>
	        {

3. Cuando haya terminado, compruebe que el archivo tenga un aspecto parecido al de la imagen siguiente.

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-enabled.png)

### Prueba de la aplicación de API

1. Para ver la página de prueba de API, ejecute la aplicación de forma local mediante **&lt;Ctrl>F5**. Aparecerá un error similar al que se muestra en la imagen siguiente.

	![](./media/app-service-api-hybrid-on-premises-sql-server/error-forbidden.png)

2. En la barra de direcciones del explorador, agregue `/swagger` al final de la dirección URL y pulse **&lt;ENTRAR>**. Se mostrará la interfaz de usuario de Swagger habilitada en la sección anterior.

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui.png)

3. Haga clic en la sección **Speakers** para expandirla.

4. Haga clic en **GET /api/speakers** para expandir esa sección.

5. Haga clic en **¡Pruébelo!** para ver los datos especificados anteriormente en la base de datos.

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out.png)

### Implementación de la aplicación de API

Ahora que ha probado la aplicación localmente, es hora de implementarla en Azure.

1. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto (no la solución) y en **Publicar...**. 

	![Opción de menú de publicación de proyecto](./media/app-service-api-hybrid-on-premises-sql-server/publish-menu.png)

2. Haga clic en la pestaña **Perfil** y en **Aplicaciones de API de Microsoft Azure (vista previa)**.

	![Opción de menú de publicación de proyecto](./media/app-service-api-hybrid-on-premises-sql-server/publish-web.png)

3. Haga clic en **Nuevo** para aprovisionar una nueva aplicación de API en su suscripción de Azure.

	![Selección del cuadro de diálogo Servicios de API existentes](./media/app-service-api-hybrid-on-premises-sql-server/publish-to-existing-api-app.png)

4. En el cuadro de diálogo **Crear una aplicación de API**, escriba lo siguiente:

	- En **Nombre de la aplicación de API**, escriba un nombre para la aplicación. 
	- Si tiene varias suscripciones de Azure, seleccione la que desee usar.
	- En el **Plan del Servicio de aplicaciones**, seleccione de entre los planes del Servicio de aplicaciones o elija **Crear nuevo plan del Servicio de aplicaciones** y escriba el nombre de un nuevo plan. 
	- En **Grupo de recursos**, seleccione de entre los grupos de recursos existentes o elija **Crear nuevo grupo de recursos** y escriba un nombre. El nombre debe ser único; considere la posibilidad de usar el nombre de la aplicación como un prefijo y anexe información personal como el identificador de Microsoft (sin el signo @).  
	- En **Nivel de acceso**, seleccione **Disponible para cualquier persona**. Con esta opción, la API será totalmente pública, lo cual está bien para este tutorial. Puede restringir el acceso más adelante a través del [Portal de vista previa de Azure](https://portal.azure.com/).
	- Seleccione una región.

	Haga clic en **Aceptar** para crear la aplicación de API en su suscripción.

	![Configuración del cuadro de diálogo de Aplicación web de Microsoft Azure](./media/app-service-api-hybrid-on-premises-sql-server/publish-new-api-app.png)

5. El proceso puede tardar unos minutos, por lo que Visual Studio muestra un cuadro de diálogo que le notifica que se ha iniciado el proceso. Haga clic en **Aceptar**, en el cuadro de diálogo de confirmación.

	![Mensaje de confirmación de inicio de creación de Servicio de API](./media/app-service-api-hybrid-on-premises-sql-server/create-api-app-confirmation.png)

6. Mientras que el proceso de aprovisionamiento crea el grupo de recursos y la aplicación de API en la suscripción de Azure, Visual Studio muestra el progreso en la ventana **Actividad del Servicio de aplicaciones de Azure**.

	![Notificación de estado mediante la ventana de actividad del Servicio de aplicaciones de Azure](./media/app-service-api-hybrid-on-premises-sql-server/app-service-activity.png)

7. Una vez que se ha aprovisionado la aplicación de API, haga clic con el botón secundario en el proyecto en el **Explorador de soluciones** y seleccione **Publicar** para volver a abrir el cuadro de diálogo de publicación. El perfil de publicación que creó en el paso anterior debe estar preseleccionado. Haga clic en **Publicar** para comenzar el proceso de implementación.

	![Implementación de la aplicación de API](./media/app-service-api-hybrid-on-premises-sql-server/publish2.png)

En la ventana **Actividad del Servicio de aplicaciones de Azure** se muestra el progreso de la implementación y se indicará cuándo se completa el proceso de publicación.

## Creación de una conexión híbrida y un servicio de BizTalk ##

1. En el explorador, vaya al [Portal de vista previa de Azure](https://portal.azure.com/). 

2. Haga clic en la opción **Examinar todo** a la izquierda.

3. En la hoja **Examinar**, seleccione **Aplicaciones de API**.

4. En la hoja **Aplicaciones de API**, localice la aplicación de API y haga clic en ella.

5. En la hoja de la aplicación de API, haga clic en el valor de **Host de aplicaciones API**.
 
	![Hoja Aplicación de API](./media/app-service-api-hybrid-on-premises-sql-server/api-app-blade-api-app-host.png)

6. Cuando se abra la hoja **Host de aplicaciones API**, desplácese hacia abajo hasta la sección **Redes** y haga clic en **Conexiones híbridas**.
	
	![Hybrid connections](./media/app-service-api-hybrid-on-premises-sql-server/api-app-host-blade-hybrid-connections.png)
	
7. En la hoja **Conexiones híbridas**, haga clic en **Agregar** > **Nueva conexión híbrida**.
	
8. En la **hoja Crear conexión híbrida**:
	- En **Nombre**, proporcione un nombre para la conexión.
	- En **Nombre de host**, escriba el nombre del equipo del equipo host de SQL Server.
	- En **Puerto**, escriba `1433` (el puerto predeterminado para SQL Server).
	- Haga clic en **Servicio de Biz Talk** y escriba un nombre para el servicio de BizTalk.
	
	![Create a hybrid connection](./media/app-service-api-hybrid-on-premises-sql-server/create-biztalk-service.png)
		
9. Haga clic en **Aceptar** dos veces.

	Al finalizar el proceso, el área **Notificaciones** mostrará una notificación de **ÉXITO** parpadeante de color verde y la hoja **Conexión híbrida** mostrará la conexión híbrida nueva con el estado **No conectado**.
	
	![Conexión híbrida creada](./media/app-service-api-hybrid-on-premises-sql-server/hybrid-not-connected-yet.png)
	
Llegados a este punto, ha completado una parte importante de la infraestructura de conexión híbrida en la nube. A continuación, creará la parte local correspondiente.

<a name="InstallHCM"></a>
## Instalación del Administrador de conexiones híbridas local para realizar la conexión

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

Ahora que la infraestructura de la conexión híbrida se ha completado, es el momento de probar la aplicación.

<a name="CreateASPNET"></a>
## Prueba de la aplicación de API finalizada en Azure

1. En el portal de vista previa de Azure, vuelva a la hoja Host de aplicaciones de API y haga clic en el valor de **URL**.
	
2. Cuando se abre la página del host de aplicaciones de API en el explorador, anexe `/swagger` a la URL en la barra de direcciones del explorador y presione **&lt;ENTRAR>**.
	
3. Haga clic en la sección **Speakers** para expandirla.

4. Haga clic en **GET /api/speakers** para expandir esa sección.

5. Haga clic en **¡Pruébelo!** para ver los datos especificados anteriormente en la base de datos.

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out-azure.png)
	
**¡Enhorabuena!** Ha creado una aplicación de API que se ejecuta en Azure y utiliza una conexión híbrida para conectarse a una base de datos de SQL Server en instalaciones locales.

## Otras referencias
[Introducción a las conexiones híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist presenta las conexiones híbridas (vídeo de Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Introducción a las conexiones híbridas](/services/biztalk-services/)

[Servicios de BizTalk: pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas](../biztalk-dashboard-monitor-scale-tabs/)

[Creación de una nube híbrida del mundo real con una perfecta portabilidad de aplicaciones (vídeo de Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Conexión a un servidor SQL Server local desde un servicio móvil de Azure mediante Conexiones híbridas](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)

[Conexión a un servidor SQL Server local desde Servicios móviles de Azure mediante conexiones híbridas (vídeo de Canal 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[Introducción a la identidad de ASP.NET](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

<!---HONumber=AcomDC_0128_2016-->