<properties 
	pageTitle="Depuración de una aplicación de API en el Servicio de aplicaciones de Azure" 
	description="Aprenda a crear una aplicación de API mientras se ejecuta en el Servicio de aplicaciones de Azure, con Visual Studio." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Depuración de una aplicación de API en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Información general

En este tutorial, depurará código de API de web de ASP.NET que se ha configurado para ejecutarse en una [aplicación de API](app-service-api-apps-why-best-platform.md) en el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md). Depure el código mientras se está ejecutando localmente y mientras se ejecuta de forma remota en Azure.

El tutorial funciona con la aplicación de la API que ha [creado](app-service-dotnet-create-api-app.md) e [implementado](app-service-dotnet-deploy-api-app.md) en los tutoriales anteriores de esta serie.

## Depuración de una aplicación de API de forma remota 

Para habilitar la depuración remota, debe implementar una compilación de depuración en Azure.

1. En el **Explorador de soluciones** de Visual Studio, haga clic con el botón derecho en el proyecto que implementó en [el tutorial anterior](app-service-dotnet-deploy-api-app.md) y seleccione **Publicar**.

2. En el cuadro de diálogo **Publicación web**, seleccione la pestaña **Configuración** y seleccione la configuración de compilación **Depurar**.

4. Haga clic en **Publicar**.

	![Publicar proyecto](./media/app-service-api-dotnet-debug/rd-debug-publish.png)

	Se abre una ventana del explorador en la URL base de la aplicación de API.

4. En la barra de direcciones del explorador, agregue /swagger al final de la dirección URL y presione Entrar.

	Para este paso, se supone que habilitó la interfaz de usuario de Swagger tal como se indicó en el tutorial de [creación](app-service-dotnet-create-api-app.md).

	![Swagger UI](./media/app-service-api-dotnet-debug/rd-swagger-ui.png)

5. Vuelva a Visual Studio y haga clic desde **Ver > Explorador de servidores**.

6. En la ventana **Explorador de servidores**, expanda el nodo **Azure > Servicio de aplicaciones**.

7. Localice el grupo de recursos que creó o seleccionó cuando implementó la aplicación de API.

8. En el grupo de recursos, haga clic con el botón secundario en el nodo de la aplicación de API y seleccione **Asociar depurador**.

	![Asociación del depurador](./media/app-service-api-dotnet-debug/rd-attach-debugger.png)

	El depurador remoto intentará conectarse. En algunos casos, puede que necesite volver a hacer clic en **Asociar depurador** para establecer una conexión, por lo que si se produce un error, vuelva a intentarlo.

	![Asociación del depurador](./media/app-service-api-dotnet-debug/rd-attaching.png)

9. Una vez establecida la conexión, abra el archivo **ContactsController.cs** en el proyecto de la aplicación de API y agregue un punto de interrupción en el método `Get`.

	![Aplicación de puntos de interrupción al controlador](./media/app-service-api-dotnet-debug/rd-breakpoints.png)

10. Vuelva a la sesión del explorador, haga clic en el verbo **Obtener** para mostrar el esquema del objeto *Contacto* y, a continuación, haga clic en **Probar**. Visual Studio detiene la ejecución del programa en el punto de interrupción y así puede depurar la lógica del controlador.

	![Prueba](./media/app-service-api-dotnet-debug/rd-try-it-out.png)

## Depuración de una aplicación de API de forma local 

Puede haber ocasiones en que desee depurar la aplicación de API de manera local; por ejemplo, si las acciones de ida y vuelta desde y hasta el servidor de Azure ralentizan la depuración. En esta sección, se muestra cómo depurar la aplicación de API de manera local mediante la interfaz de usuario de Swagger como el cliente de prueba.

2. En el explorador, vaya al [Portal de vista previa de Azure](https://portal.azure.com/). 

3. Haga clic en el botón **Examinar** de la barra lateral y seleccione **Aplicaciones de API**.

	![Examen de opciones en el portal de Azure](./media/app-service-api-dotnet-debug/ld-browse.png)

4. Seleccione la aplicación de API que creó en la lista de aplicaciones de API de su suscripción.

	![Lista de aplicaciones de API](./media/app-service-api-dotnet-debug/ld-api-app-list.png)

5. En la hoja Aplicación de API, haga clic en **Host de aplicación de API**.

	![Host de aplicación de API](./media/app-service-api-dotnet-debug/ld-api-app-blade-api-app-host.png)

6. En la hoja del host de aplicación de API, haga clic en **Todas las configuraciones**.

	![Host de aplicación de API: todas las configuraciones](./media/app-service-api-dotnet-debug/ld-api-app-host-all-settings.png)

7. Vuelva a la hoja **Configuración** y haga clic en **Configuración de la aplicación**.

	![Host de aplicación de API: configuraciones de la aplicación](./media/app-service-api-dotnet-debug/ld-application-settings.png)

8. En la hoja **Configuración de la aplicación web**, acceda a la sección **Configuración de la aplicación**.

	![Host de aplicación de API: configuración de aplicaciones para depuración local](./media/app-service-api-dotnet-debug/ld-app-settings-for-local-debugging.png)

1. En Visual Studio, abra el archivo *web.config* del proyecto de aplicación de API.

9. En **Configuración de aplicaciones**, agregue cada uno de los siguientes valores y claves al archivo *web.config* en la sección **appSettings**.
	- **EMA\_MicroserviceId**
	- **EMA\_Secret**
	- **EMA\_RuntimeUrl**

	Cuando termine, la sección **appSettings** del archivo *web.config* debería ser similar a la captura de pantalla siguiente.

	![Host de aplicación de API: configuración de aplicaciones para depuración local](./media/app-service-api-dotnet-debug/ld-debug-settings.png)

	**Nota:** los valores *EMA\_* que ha agregado al archivo *web.config* de esta sección contienen información confidencial. Por lo tanto, se recomienda que evite guardar estos valores en un repositorio de control de código fuente público. Si desea obtener más información, consulte el artículo [Best practices for deploying passwords and other sensitive data to ASP.NET and Azure App Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) (Procedimientos recomendados para implementar contraseñas y otra información confidencial en ASP.NET y Servicio de aplicaciones de Azure).

10. Coloque un punto de interrupción en el código del controlador de la aplicación de API del método `Get`.

11. Presione F5 para iniciar una sesión de depuración de Visual Studio.
 
13.  Si se establece el nivel de acceso de la aplicación de API en **Público (anónimo)**, puede usar la página de la interfaz de usuario de Swagger para probar.

	* Cuando el explorador carga la página, se muestra una página de error HTTP 403. En la barra de direcciones del explorador, agregue */swagger* al final de la dirección URL y presione Entrar.

	* Una vez que se haya cargado Swagger UI, haga clic en el verbo **Obtener** para mostrar el esquema del objeto Contacto y, a continuación, haga clic en **Probar**.

		Visual Studio detiene la ejecución del programa en el punto de interrupción y así puede depurar la lógica del controlador.

14.	Si se establece el nivel de acceso de la aplicación de API en **Público (autenticado)**, necesitará autenticar y usar una herramienta de explorador siguiendo los procedimientos que se muestran en [Protección de una aplicación de API](app-service-api-dotnet-add-authentication.md#use-postman-to-send-a-post-request) para una solicitud Post, es decir:

	* Vaya a la dirección URL de inicio de sesión de la puerta de enlace y escriba las credenciales para iniciar sesión.
	* Obtenga el valor de token de Zumo de la cookie x-zumo-auth.
	* Agregue un encabezado x-zumo-auth a la solicitud y establezca su valor con el valor de la cookie x-zumo-auth.
	* Envíe la solicitud.

	**Nota:** cuando la ejecución se realiza localmente, Azure no puede controlar el acceso a la aplicación de API para asegurarse de que solo los usuarios autenticados pueden ejecutar sus métodos. En cambio, si la ejecución se realiza en Azure, todo el tráfico destinado a la aplicación de API se enruta a través de la puerta de enlace y la puerta de enlace no pasa las solicitudes no autenticadas. No hay ninguna redirección cuando la ejecución se realiza localmente, lo que significa que a las solicitudes no autenticadas no se les impide tener acceso a la aplicación de API. El valor de autenticación, como se ha descrito anteriormente, es que se puede ejecutar correctamente código relacionado con la autenticación en la aplicación de API, como el código que recupera información sobre el usuario que ha iniciado sesión. Para obtener más información sobre cómo la puerta de enlace controla la autenticación para aplicaciones de API, consulte [Autenticación para aplicaciones de API y aplicaciones móviles](../app-service/app-service-authentication-overview.md#azure-app-service-gateway).

## Pasos siguientes

En este tutorial, vio cómo depurar las aplicaciones de API.

Para obtener más información sobre cómo solucionar problemas, consulte [Solución de problemas de una aplicación web en el Servicio de aplicaciones de Azure con Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md). Debido a que las aplicaciones de API son aplicaciones web que tienen características adicionales para el hospedaje de servicios web, puede utilizar las mismas herramientas de depuración y de solución de problemas para aplicaciones de API que se usan para las aplicaciones web.

<!---HONumber=AcomDC_0128_2016-->