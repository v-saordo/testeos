<properties
	pageTitle="Autenticación de entidad de servicio para Aplicaciones de API en el Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Aprenda a proteger una aplicación de API en el Servicio de aplicaciones de Azure para escenarios entre servicios."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/26/2016" 
	ms.author="tdykstra"/>

# Autenticación de entidad de servicio para Aplicaciones de API en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [selector](../../includes/app-service-api-auth-selector.md)]

## Información general

En este artículo, aprenderá lo siguiente:

* Usar Azure Active Directory (Azure AD) para proteger una aplicación de API de accesos no autenticados.
* Usar una aplicación de API protegida mediante credenciales de entidad de servicio (identidad de aplicación).
* Asegurarse de que los usuarios que iniciaron sesión no pueda llaman a la aplicación de API protegida desde un explorador.
* Asegurarse de que solo una entidad de servicio de Azure AD pueda llamar a la aplicación de API protegida.

Este método de protección de una aplicación de API se usa normalmente para [escenarios internos](app-service-api-authentication.md#internal), como para la realización de llamadas de una aplicación de API a otra.

El artículo contiene dos secciones:

* La sección [Configuración de la autenticación de entidad de servicio en el Servicio de aplicaciones de Azure](#authconfig) explica en términos generales cómo configurar la autenticación para cualquier aplicación de API y cómo usar la aplicación de API protegida. Esta sección se aplica igualmente a todos los marcos admitidos por el Servicio de aplicaciones, como. NET, Node.js y Java.

* El [resto del artículo](#tutorialstart) le guiará en el proceso de configuración de un escenario de "acceso interno" para una aplicación de ejemplo de .NET que se ejecuta en el Servicio de aplicaciones.

## <a id="authconfig"></a> Configuración de la autenticación de entidad de servicio en el Servicio de aplicaciones de Azure

Esta sección proporciona instrucciones generales que se aplican a cualquier aplicación de API. Para obtener los pasos específicos para la aplicación de ejemplo de .NET To Do List, vaya a [Continuación de los tutoriales de introducción de .NET](#tutorialstart).

1. En el [Portal de Azure](https://portal.azure.com/), vaya a la hoja **Aplicación de API** de la aplicación de API que desea proteger y, después, haga clic en **Configuración**.

2. En la hoja **Configuración**, busque la sección **Características** y haga clic en **Autenticación/autorización**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. En la hoja **Autenticación/autorización**, haga clic en **Activado**.

4. En la lista desplegable **Acción necesaria cuando la solicitud no está autenticada**, seleccione **Iniciar sesión con Azure Active Directory**.

5. En **Proveedores de autenticación**, seleccione **Azure Active Directory**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Configurar la hoja **Configuración de Azure Active Directory** para crear una nueva aplicación de Azure AD, o use una aplicación de Azure AD existente si ya tiene una que quiere usar.

	En los escenarios internos normalmente hay una aplicación de API que llama a una aplicación de API. Puede usar aplicaciones de AD independientes para cada aplicación de API o una sola aplicación de Azure AD.

	Para obtener instrucciones detalladas sobre esta hoja, consulte [Configuración de la aplicación del Servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

7. Cuando termine con la hoja de configuración del proveedor de autenticación, haga clic en **Aceptar**.

7. En la hoja **Autenticación/autorización**, haga clic en **Guardar**.

Al hacerlo, el Servicio de aplicaciones impide que las llamadas a API no autenticadas tengan acceso a la aplicación de API. No se requiere ningún código de autenticación ni autorización en la aplicación de API protegida.

Esta funcionalidad de autenticación funciona de la misma manera para todos los lenguajes que el servicio de aplicaciones admite, como .NET, Node.js y Java.

#### Uso de la aplicación de API protegida

El llamador debe proporcionar un token de portador de Azure AD con llamadas a API. Para obtener un token de portador con las credenciales de entidad de servicio, el llamador usa la biblioteca de autenticación de Active Directory (ADAL para [.NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory), [Node.js](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) o [Java](https://github.com/AzureAD/azure-activedirectory-library-for-java)). Para obtener un token, el código que llama a ADAL proporciona a ADAL la siguiente información:

* El nombre de su inquilino de Azure AD.
* El identificador de cliente y el secreto del cliente (clave de aplicación) de la aplicación de Azure AD asociada al llamador.
* El identificador de cliente de la aplicación de Azure AD asociada con la aplicación de API protegida. (Si se usa una sola aplicación de Azure AD, es el mismo identificador de cliente que el llamador).

Estos valores están disponibles en las páginas de Azure AD del [Portal de Azure clásico](https://manage.windowsazure.com/).

Una vez adquirido el token, el llamador lo incluye con las solicitudes HTTP en el encabezado de autorización. El Servicio de aplicaciones valida el token y permite que las solicitudes lleguen a la aplicación de API protegida.

#### Protección de la aplicación de API del acceso de usuarios en el mismo inquilino

Los tokens de portador para los usuarios en el mismo inquilino se consideran válidos para la aplicación de API protegida. Si desea asegurarse de que solo una entidad de servicio pueda llamar a la aplicación de API protegida, agregue el código en la aplicación de API protegida para comprobar las siguientes notificaciones:

* `appid` debe ser el mismo que el identificador de cliente de la aplicación de Azure AD que está asociada con el llamador.
* `objectidentifier` debe ser el identificador de entidad de servicio del llamador.

### Protección de la aplicación de API de accesos desde el explorador

Si no valida las notificaciones en el código en la aplicación de API protegida y si usa otra aplicación de Azure AD para la aplicación de API protegida, asegúrese de que la dirección URL de respuesta de la aplicación de Azure AD no sea igual que la dirección URL base de la aplicación de API. Si la dirección URL de respuesta apunta directamente a la aplicación de API protegida, un usuario en el mismo inquilino de Azure AD podría usar un explorador para llegar a la aplicación de API, iniciar sesión y llamar correctamente a la API.

## <a id="tutorialstart"></a> Continuación de los tutoriales de introducción de .NET

Si está siguiendo las series de introducción de Node.js o de Java para aplicaciones de API, vaya a la sección [Siguientes pasos](#next-steps).

El resto de este artículo continúa la serie de introducción de .NET para aplicaciones de API y da por hecho que ha completado el [tutorial de autenticación de usuario](app-service-api-user-principal-authentication.md) y que tiene la aplicación de ejemplo en ejecución en Azure con la autenticación de usuario habilitada.

## Configuración de la autenticación en Azure

En esta sección, configurará el Servicio de aplicaciones para que solo las solicitudes HTTP que tengan tokens de portador de Azure AD válidos tengan acceso a la aplicación de API de capa de datos.

En la siguiente sección, configurará la aplicación de API de nivel intermedio para enviar las credenciales de la aplicación a Azure AD, obtener un token de portador y enviar el token de portador a la aplicación de API de capa de datos. Este proceso se ilustra en el diagrama.

![](./media/app-service-api-dotnet-service-principal-auth/appdiagram.png)

1. En el [Portal de Azure](https://portal.azure.com/), vaya a la hoja Aplicación de API de la aplicación de API que creó para ToDoListDataAPI (capa de datos) y después haga clic en **Configuración**.

2. En la hoja **Configuración**, busque la sección **Características** y haga clic en **Autenticación/autorización**.

3. En la hoja **Autenticación/autorización**, haga clic en **Activado**.

4. En la lista desplegable **Acción por realizar cuando no se autentique la solicitud**, seleccione **Iniciar sesión con Azure Active Directory**.

	Esta es la configuración que hace que el Servicio de aplicaciones se asegure de que solo las solicitudes autenticadas alcancen la aplicación de API. Para las solicitudes que tienen tokens de portador válidos, el Servicio de aplicaciones pasa los tokens a la aplicación de API y rellena los encabezados HTTP con las notificaciones usadas normalmente para que esa información esté más fácilmente disponible para el código.

5. En **Proveedores de autenticación**, haga clic en **Azure Active Directory**.

6. En la hoja **Configuración de Azure Active Directory**, haga clic en **Rápida**.

	Con la opción **Express**, Azure puede crear automáticamente una aplicación de AAD en su [inquilino](https://msdn.microsoft.com/es-ES/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) de Azure AD.

	No es preciso que cree un inquilino, porque todas las cuenta de Azure tienen uno automáticamente.

7. En **Modo de administración**, haga clic en **Crear nueva aplicación de AD**.

	El portal conecta el cuadro de entrada **Crear aplicación** con un valor predeterminado.
	
	![](./media/app-service-api-dotnet-service-principal-auth/aadsettings.png)

	De forma predeterminada, la aplicación de Azure AD recibe el mismo nombre que la aplicación de API. Si lo prefiere, escriba un nombre diferente.

	Como alternativa, podría usar un sola aplicación de Azure AD para la aplicación de API que realiza la llamada y la aplicación de API protegida. Si eligió esa alternativa, no necesitaría la opción **Crear nueva aplicación de AD** aquí porque ya creó una aplicación de Azure AD anteriormente en el tutorial autenticación de usuarios. Para este tutorial, usará aplicaciones diferentes de Azure AD para la aplicación que realiza la llamada de API y la aplicación de API protegida.

8. Anote el valor del cuadro de entrada **Crear aplicación**; consultará esta aplicación AAD más adelante en el Portal de Azure clásico.

7. Haga clic en **Aceptar**.

10. En la hoja **Autenticación/autorización**, haga clic en **Guardar**.

	![](./media/app-service-api-dotnet-service-principal-auth/saveauth.png)

	El Servicio de aplicaciones crea una aplicación de Azure Active Directory con **URL de inicio de sesión** y **URL de respuesta** establecidas automáticamente en la URL de la aplicación de API. El último valor permite a los usuarios del inquilino de AAD iniciar sesión y tener acceso a la aplicación de API.

### Comprobación de la protección de la aplicación de API

1. En un explorador, vaya a la dirección URL de la aplicación de API: en la hoja **Aplicación de API** del Portal de Azure, haga clic en el vínculo en **URL**. 

	Se le redirigirá a una pantalla de inicio de sesión porque no se permite que las solicitudes no autenticadas tengan acceso a la aplicación de API.

	Si el explorador va a la interfaz de usuario de Swagger, es posible que el explorador ya iniciara sesión; en ese caso, abra una ventana de InPrivate o Incognito y vaya a la dirección URL de la interfaz de usuario de Swagger.

18. Inicie sesión con las credenciales de un usuario en su inquilino de AAD.

	Una vez iniciada la sesión, en el explorador aparece una página que indica que "se creó correctamente".

## Configuración del proyecto ToDoListAPI para adquirir y enviar el token de Azure AD

En esta sección realizará las siguientes tareas:

* Agregar el código en la aplicación de API de nivel intermedio que usa credenciales de la aplicación de Azure AD para adquirir un token y enviarlo con las solicitudes HTTP a la aplicación de API de capa de datos.
* Obtener las credenciales que necesita de Azure AD.
* Escribir las credenciales en la configuración del entorno en tiempo de ejecución de Servicio de aplicaciones de Azure en la aplicación de API de nivel intermedio. 

### Configuración del proyecto ToDoListAPI para adquirir y enviar el token de Azure AD

Realice los cambios siguientes en el proyecto ToDoListAPI en Visual Studio.

1. Quite las marcas de comentarios de todo el código en el archivo *ServicePrincipal.cs*.

	Este es el código que usa ADAL para .NET para adquirir el token de portador de Azure AD. Usa varios valores de configuración que se establecerán más adelante en el entorno en tiempo de ejecución de Azure. Este es el código:

		public static class ServicePrincipal
		{
		    static string authority = ConfigurationManager.AppSettings["ida:Authority"];
		    static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
		    static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
		    static string resource = ConfigurationManager.AppSettings["ida:Resource"];
		
		    public static AuthenticationResult GetS2SAccessTokenForProdMSA()
		    {
		        return GetS2SAccessToken(authority, resource, clientId, clientSecret);
		    }
		
		    static AuthenticationResult GetS2SAccessToken(string authority, string resource, string clientId, string clientSecret)
		    {
		        var clientCredential = new ClientCredential(clientId, clientSecret);
		        AuthenticationContext context = new AuthenticationContext(authority, false);
		        AuthenticationResult authenticationResult = context.AcquireToken(
		            resource,
		            clientCredential);
		        return authenticationResult;
		    }
		}

	**Nota:** Este código requiere ADAL para el paquete de NuGet de .NET (Microsoft.IdentityModel.Clients.ActiveDirectory), que ya está instalado en el proyecto. Si estuviese creando este proyecto desde cero, tendría que instalar este paquete. La plantilla de nuevo proyecto de aplicación de API no instala este paquete automáticamente.

2. En *Controllers/ToDoListController*, quite los comentarios del código en el método `NewDataAPIClient` que agrega el token a las solicitudes HTTP en el encabezado de autorización.

		client.HttpClient.DefaultRequestHeaders.Authorization =
		    new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);

3. Implemente el proyecto ToDoListAPI. (Haga clic con el botón derecho en el proyecto y haga clic en **Publicar > Publicar**).

4. Cierre la ventana del explorador que se abre después de que el proyecto se implemente correctamente.

### Obtención de los valores de configuración de Azure AD

11. En el [Portal de Azure clásico](https://manage.windowsazure.com/), vaya a **Azure Active Directory**.

12. En la pestaña **Directorio**, haga clic en su inquilino de AAD.

14. Haga clic en **Aplicaciones > Aplicación propiedad de mi compañía** y después clic en la marca de verificación.

15. En la lista de aplicaciones, haga clic en el nombre de la que Azure creó automáticamente cuando habilitó la autenticación para la aplicación de API ToDoListDataAPI (capa de datos).

16. Haga clic en la pestaña **Configurar**.

5. Copie el valor de **Id. de cliente** y guárdelo en un lugar donde pueda recuperarlo más adelante.

8. En el Portal de Azure clásico, vuelva a la lista **Aplicaciones que tiene mi compañía**, y haga clic en la aplicación de AAD que creó para la aplicación de API ToDoListAPI (nivel intermedio).

16. Haga clic en la pestaña **Configurar**.

5. Copie el valor de **Id. de cliente** y guárdelo en un lugar donde pueda recuperarlo más adelante.

6. En **Claves**, seleccione **1 año** en la lista desplegable **Seleccionar duración**.

6. Haga clic en **Guardar**.

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. Copie el valor de clave y guárdelo en un lugar donde pueda recuperarlo más adelante.

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

### Configuración de opciones de Azure AD en el entorno en tiempo de ejecución de la aplicación de API de nivel intermedio

1. Vaya al [Portal de Azure](https://portal.azure.com/) y, después, vaya a la hoja **Aplicación de API** de la aplicación de API que hospeda el proyecto TodoListAPI (nivel intermedio).

2. Haga clic en **Configuración > Configuración de la aplicación**.

3. En la sección **Configuración de la aplicación**, agregue las siguientes claves y valores:

	|Clave|Valor|Ejemplo
	|---|---|---|
	|ida:Authority|https://login.microsoftonline.com/{your nombre del inquilino de Azure AD}|https://login.microsoftonline.com/contoso.onmicrosoft.com|
	|ida:ClientId|Id. de cliente de la aplicación que realiza la llamada (ToDoListAPI)|960adec2-b74a-484a-960adec2-b74a-484a|
	|ida:ClientSecret|Clave de aplicación de la aplicación que realiza la llamada (ToDoListAPI)|oCgdj3EYLfnR0p6iR3UvHFAfkn+zQB+0VqZT/6=
	|ida:Resource|Id. de cliente de la aplicación a la que se llama (ToDoListDataAPI)|e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8|

	Si usaba un sola aplicación de Azure AD para la aplicación de API que realiza la llamada y la aplicación de API protegida, usaría el mismo valor tanto en `ida:ClientId` como en `ida:Resource`.

	El código usa ConfigurationManager para obtener estos valores, por lo que podría almacenarse en el archivo Web.config del proyecto o en el entorno en tiempo de ejecución de Azure. Mientras se ejecuta una aplicación ASP.NET en el Servicio de aplicaciones de Azure, la configuración del entorno invalida automáticamente la configuración de Web.config. La configuración del entorno suele ser una [manera más segura de almacenar información confidencial comparado con un archivo Web.config](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

6. Haga clic en **Guardar**.

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

### Prueba de la aplicación

1. En un explorador, vaya a la dirección URL HTTPS de la aplicación web de front-end AngularJS.

2. Haga clic en la pestaña **To Do List** e inicie sesión con las credenciales de un usuario en el inquilino de Azure AD.

4. Agregue elementos de tareas pendientes para comprobar que la aplicación funciona.

	![](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

	Si la aplicación no funciona como se espera, vuelva a comprobar todos los valores que escribió en el Portal de Azure. Si toda la configuración parece ser correcta, consulte la sección [Solución de problemas](#troubleshooting) más adelante en este tutorial.

## Protección de la aplicación de API contra el acceso desde el explorador

Para este tutorial, creó una aplicación de Azure AD independiente para la aplicación de API ToDoListDataAPI (capa de datos). Como hemos visto, cuando el Servicio de aplicaciones crea una aplicación AAD, la configura de manera que permite a un usuario ir a la dirección URL de la aplicación de API en un explorador e iniciar sesión. Esto significa no solo una entidad de servicio puede tener acceso a la API, también un usuario final de su inquilino de Azure AD.

Si quiere impedir el acceso desde el explorador sin escribir ningún código en la aplicación de la API protegida, puede cambiar la **URL de respuesta** en la aplicación AAD para que sea diferente de la dirección URL base de la aplicación de API.

### Deshabilitación del acceso desde el explorador

1. En la pestaña **Configurar** del portal clásico para la aplicación AAD que se creó para TodoListService, modifique el valor del campo **URL de respuesta** para que sea una dirección URL válida, pero no la URL de la aplicación de API.
 
2. Haga clic en **Guardar**.

### Comprobación de que el acceso desde el explorador ya no funciona

Antes comprobó que puede ir a la URL de la aplicación de API desde un explorador iniciando sesión con las credenciales de un usuario individual. En esta sección, comprobará que ya no es posible.

1. En una nueva ventana del explorador, vaya de nuevo a la dirección URL de la aplicación de API.

2. Inicie la sesión cuando se le solicite.

3. El inicio de sesión se realiza correctamente, pero genera una página de error.

	Configuró la aplicación AAD para que los usuarios del inquilino de AAD no puedan iniciar sesión ni acceder a la API desde un explorador. Aún puede acceder a la aplicación de API con un token de entidad de servicio; para comprobarlo, vaya a la URL de la aplicación web y agregue más elementos de tareas pendientes.

## Restricción del acceso a una entidad de servicio determinada  

En este momento, cualquier llamador que pueda obtener un token para un usuario o entidad de servicio del inquilino de Azure AD puede llamar a la aplicación de API TodoListDataAPI (capa de datos). Puede que quiera asegurarse de que la aplicación de API de capa de datos solo acepta llamadas de la aplicación de API TodoListAPI (nivel intermedio) y solo de una entidad de servicio determinada.

Para agregar estas restricciones, puede agregar código para validar las notificaciones de `appid` y `objectidentifier` en las llamadas entrantes.

Para este tutorial, coloque código que valida el identificador de la aplicación y el identificador de la entidad de servicio directamente en las acciones de controlador. Las alternativas son usar un atributo `Authorize` personalizado o realizar esta validación en las secuencias de inicio (por ejemplo, middleware OWIN).

Realice los cambios siguientes en el proyecto TodoListDataAPI.

2. Abra el archivo *Controllers/TodoListController.cs*.

3. Quite los comentarios de las líneas que establecen `trustedCallerClientId` y `trustedCallerServicePrincipalId`.

		private static string trustedCallerClientId = ConfigurationManager.AppSettings["todo:TrustedCallerClientId"];
		private static string trustedCallerServicePrincipalId = ConfigurationManager.AppSettings["todo:TrustedCallerServicePrincipalId"];

4. Quite los comentarios del código en el método CheckCallerId. Este método se llama al principio de cada método de acción en el controlador.

		private static void CheckCallerId()
		{
		    string currentCallerClientId = ClaimsPrincipal.Current.FindFirst("appid").Value;
		    string currentCallerServicePrincipalId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		    if (currentCallerClientId != trustedCallerClientId || currentCallerServicePrincipalId != trustedCallerServicePrincipalId)
		    {
		        throw new HttpResponseException(new HttpResponseMessage { StatusCode = HttpStatusCode.Unauthorized, ReasonPhrase = "The appID or service principal ID is not the expected value." });
		    }
		}

5. Vuelva a implementar el proyecto ToDoListDataAPI en el Servicio de aplicaciones de Azure.

6. En el explorador, vaya a la dirección URL HTTPS de la aplicación web front-end AngularJS y, en la página principal, haga clic en la pestaña **To Do List**.

	La aplicación no funciona porque se producen errores en llamadas al back-end. El nuevo código está comprobando los valores reales de appid y objectidentifier pero todavía no tiene los valores correctos con los que compararlos. La consola de herramientas para desarrolladores del explorador informa de que el servidor devuelve un error HTTP 401.

	![](./media/app-service-api-dotnet-service-principal-auth/webapperror.png)

	En los pasos siguientes configurará los valores esperados.

8. Con Azure AD PowerShell, obtenga el valor de la entidad de servicio para la aplicación de Azure AD que creó para el proyecto TodoListWebApp.

	a. Para obtener instrucciones sobre cómo instalar Azure PowerShell y conectarse a su suscripción, consulte [Uso de Azure PowerShell con Administrador de recursos de Azure](../powershell-azure-resource-manager.md).

	b. Para obtener una lista de entidades de servicio, ejecute el comando `Login-AzureRmAccount` y, a continuación, el comando `Get-AzureRmADServicePrincipal`.

	c. Busque el valor de objectid para la entidad de servicio de la aplicación TodoListAPI y guárdelo en una ubicación que pueda copiar más adelante.

7. En el Portal de Azure, vaya a la hoja la aplicación web correspondiente a la aplicación web en la que implementó el proyecto ToDoListAngular.

9. Haga clic en **Configuración > Configuración de la aplicación**.

3. En la sección **Configuración de la aplicación**, agregue las siguientes claves y valores:

	|Clave|Valor|Ejemplo
	|---|---|---|
	|todo:TrustedCallerServicePrincipalId|Id. de entidad de servicio de la aplicación que realiza la llamada|4f4a94a4-6f0d-4072-4f4a94a4-6f0d-4072|
	|todo:TrustedCallerClientId|Id. de cliente de la aplicación que realiza la llamada: copiado de la aplicación AAD TodoListAPI|960adec2-b74a-484a-960adec2-b74a-484a|

6. Haga clic en **Guardar**.

	![](./media/app-service-api-dotnet-service-principal-auth/trustedcaller.png)

6. En el explorador, vuelva a la dirección URL de la aplicación web y, en la página principal, haga clic en la pestaña **To Do List**.

	Esta vez la aplicación funciona según lo esperado porque el identificador de la aplicación que realiza la llamada y el identificador de entidad de servicio son los valores esperados.

	![](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

## Creación de los proyectos desde cero

Los dos proyectos de Web API se crearon con la plantilla de proyecto **Aplicación de API de Azure** y reemplace el controlador de los valores predeterminados por un controlador de ToDoList. Para adquirir los tokens de entidad de servicio de Azure AD en el proyecto ToDoListAPI, se instaló el paquete de NuGet [Biblioteca de autenticación de Active Directory (ADAL) para .NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
 
Para obtener información sobre cómo crear una aplicación de una sola página AngularJS con un back-end de API Web como ToDoListAngular, consulte [Hands On Lab: Build a Single Page Application (SPA) with ASP.NET Web API and Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs) (Laboratorio práctico: Crear una aplicación de una sola página (SPA) con ASP.NET Web API y Angular.js). Para obtener información sobre cómo agregar código de autenticación de Azure AD, consulte [Seguridad de las aplicaciones de una sola página AngularJS con Azure AD](../active-directory/active-directory-devquickstarts-angular.md).

## Solución de problemas

Si la aplicación se ejecuta correctamente sin autenticación y luego no funciona al implementar la autenticación, la mayoría de las veces el problema serán opciones de configuración incorrectas o incoherentes. Para empezar, vuelva a comprobar toda la configuración del Servicio de aplicaciones de Azure y Azure Active Directory. Estas son algunas sugerencias específicas:

* Después de configurar el código en un proyecto, asegúrese de volver a implementar ese proyecto y no uno de los otros.
* Para las opciones configuradas en la hoja **Configuración de la aplicación** del Portal de Azure, asegúrese de seleccionar la aplicación de API o la aplicación web correcta cuando establezca la configuración.
* Asegúrese de que va a direcciones URL HTTPS en el explorador, no a direcciones URL HTTP.
* Asegúrese de que CORS sigue habilitado en la aplicación de API de nivel intermedio, lo que permite llamadas al nivel intermedio de la dirección URL HTTPS del front-end. Si tiene dudas acerca de si el problema está relacionado con CORS, pruebe a usar "*" como dirección URL de origen permitida. **Importante:** Algunos mensajes de error de la consola de herramientas para desarrolladores del explorador pueden notificar un error de CORS cuando el problema real es la autenticación de la capa de datos. Para comprobar si este es el caso, deshabilite temporalmente la autenticación para la aplicación de API ToDoListDataAPI.
* Para asegurarse de que obtiene tanta información como sea posible en los mensajes de error, establezca el [modo customErrors en Off (desactivado)](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* Si se producen errores en otros métodos, intente una [sesión de depuración remota](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug) y examine los valores de las variables que se pasan al código que adquiere el token de portador en ToDoListAPI, y al código que valida los valores de Azure AD recibidos en ToDoListDataAPI. Tenga en cuenta que el código puede tomar valores de configuración de muchos orígenes diferentes, por lo que es posible encontrarse sorpresas como esta. Por ejemplo, si asigna a `ida:ClientId` un nombre incorrecto como `ida:ClientID` al configurar el Servicio de aplicaciones, el código podría obtener el valor de `ida:ClientId` que está buscando en el archivo Web.config y omitir la configuración del Servicio de aplicaciones. 

## Pasos siguientes

Este es el último artículo de la serie de introducción a Aplicaciones de API.

Para más información acerca de Azure Active Directory, consulte los siguientes recursos.

* [Guía para desarrolladores de Azure AD](http://aka.ms/aaddev)
* [Escenarios de Azure AD](http://aka.ms/aadscenarios)
* [Ejemplos de Azure AD](http://aka.ms/aadsamples)

Para más información sobre otras formas de implementar proyectos de Visual Studio en aplicaciones de API, ya sea con Visual Studio o mediante la [automatización de la implementación](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) desde un [sistema de control de código fuente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), consulte [Documentación de implementación del Servicio de aplicaciones de Azure](web-sites-deploy.md).

<!---HONumber=AcomDC_0204_2016-->