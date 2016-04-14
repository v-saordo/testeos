<properties
	pageTitle="Autenticación de usuario para aplicaciones de API en el Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Obtenga información sobre cómo proteger una aplicación de API del Servicio de aplicaciones de Azure al permitir el acceso únicamente a usuarios autenticados."
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

# Autenticación de usuario para aplicaciones de API en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [selector](../../includes/app-service-api-auth-selector.md)]

## Información general

En este artículo, aprenderá lo siguiente:

* Cómo proteger una aplicación de API del Servicio de aplicaciones para que solo puedan llamarla los usuarios autenticados.
* Cómo configurar un proveedor de autenticación, con detalles para Azure Active Directory (Azure AD).
* Cómo consumir una aplicación de API protegida mediante la [Biblioteca de autenticación de Active Directory (ADAL) para JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js).

El artículo contiene dos secciones:

* La sección [Configuración de la autenticación de usuarios en el Servicio de aplicaciones de Azure](#authconfig) explica de forma general cómo configurar la autenticación de usuarios de cualquier aplicación de API y se aplica por igual a todos los marcos de trabajo compatibles con el Servicio de aplicaciones, incluyendo .NET, Node.js y Java.

* El [resto del artículo](#tutorialstart) le guiará en el proceso de configuración de una aplicación de ejemplo de .NET que se ejecuta en el Servicio de aplicaciones para que utilice Azure Active Directory para la autenticación de usuarios.

## <a id="authconfig"></a> Configuración de la autenticación de usuarios en el Servicio de aplicaciones de Azure

Esta sección proporciona instrucciones generales que se aplican a cualquier aplicación de API. Para obtener los pasos específicos para la aplicación de ejemplo de .NET To Do List, vaya a [Continuación de los tutoriales de introducción de .NET](#tutorialstart).

1. En el [Portal de Azure](https://portal.azure.com/), vaya a la hoja **Aplicación de API** de la aplicación de API que desea proteger y, después, haga clic en **Configuración**.

2. En la hoja **Configuración**, busque la sección **Características** y haga clic en **Autenticación/autorización**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. En la hoja **Autenticación/autorización**, haga clic en **Activado**.

4. Seleccione una de las opciones de la lista desplegable **Acción necesaria cuando la solicitud no está autenticada**.

	* Si desea que solo pueden acceder a la aplicación de API las llamadas que estén autenticadas, elija una de las opciones de **Iniciar sesión con**. Esta opción permite proteger la aplicación de API sin escribir código que se ejecute en ella.

	* Si desea que todas las llamadas accedan a la aplicación de API, elija **Permitir solicitud (sin acción)**. Esta opción se puede utilizar para mostrar a los autores de llamadas no autenticados los proveedores de autenticación entre los que pueden elegir. Con esta opción es preciso escribir código para controlar la autorización.

	Para más información, consulte [Autenticación y autorización para Aplicaciones de API en el Servicio de aplicaciones de Azure](app-service-api-authentication.md#multiple-protection-options).

5. Seleccione uno o varios **proveedores de autenticación**.

	La imagen muestra las opciones que requieren Azure AD autentique a que todos los autores de llamadas.
 
	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

	Al elegir un proveedor de autenticación, el portal muestra la hoja de configuración de dicho proveedor.

	Para más información sobre cómo configurar las hojas de configuración de los proveedores de autenticación, consulte [Configuración de la aplicación del Servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). (El vínculo abre un artículo sobre Azure AD, pero el propio artículo contiene pestañas con vínculos a artículos para los restantes proveedores de autenticación).

7. Cuando termine con la hoja de configuración del proveedor de autenticación, haga clic en **Aceptar**.

7. En la hoja **Autenticación/autorización**, haga clic en **Guardar**.

Al hacerlo, el Servicio de aplicaciones autentica todas las llamadas API antes de que lleguen a la aplicación de API. Estos servicios de autenticación funcionan igual en todos los lenguajes que admite el Servicio de aplicaciones, como .NET, Node.js y Java.

Para realizar llamadas API autenticadas, el autor de la llamada incluye el token de portador de OAuth 2.0 del proveedor de autenticación en el encabezado de autorización de las solicitudes HTTP. El token se puede adquirir mediante el SDK del proveedor de autenticación.

## <a id="tutorialstart"></a> Continuación de los tutoriales de introducción de .NET

Si está siguiendo la serie de introducción de Node.js o Java para Aplicaciones de API, vaya al siguiente artículo, [Autenticación de entidad de servicio para Aplicaciones de API en el Servicio de aplicaciones de Azure](app-service-api-dotnet-service-principal-auth.md).

Si está siguiendo la serie de introducción de .NET para Aplicaciones de API y ya ha implementado la aplicación de ejemplo como se indica en el [primer](app-service-api-dotnet-get-started.md) y [segundo](app-service-api-cors-consume-javascript.md) tutoriales, vaya a la sección [Configuración de autenticación](#azureauth).

Si aún no ha completado el primer y segundo tutorial y desea seguir este, utilice el botón **Implementar en Azure** en la [lista de tareas pendientes del archivo Léame del repositorio de ejemplo](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/readme.md) para implementar las aplicaciones de API y la aplicación web.

Cuando la implementación ha finalizado, se muestra un vínculo HTTP a la aplicación web. Para ejecutar la aplicación y comprobar que está operativa, cambie esa dirección URL a HTTPS.

## <a id="azureauth"></a> Configuración de la autenticación en Azure

En este momento. la aplicación se ejecuta en el Servicio de aplicaciones de Azure sin necesidad de que los usuarios se autentiquen. En esta sección se agregará la autenticación, para lo que es preciso realizar las siguientes tareas:

* Configurar el Servicio de aplicaciones para que requiera la autenticación de Azure Active Directory (Azure AD) para llamar a la aplicación de API de nivel intermedio.
* Crear una aplicación de Azure.
* Configurar la aplicación de Azure AD para enviar el token de portador después de iniciar sesión en el front-end de AngularJS. 

### Configuración de la autenticación en el Servicio de aplicaciones

1. En el [Portal de Azure](https://portal.azure.com/), vaya a la hoja **Aplicación de API** de la aplicación de API que creó para el proyecto ToDoListAPI.

2. Haga clic en **Configuración**

2. En la hoja **Configuración**, busque la sección **Características** y haga clic en **Autenticación/autorización**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. En la hoja **Autenticación/autorización**, haga clic en **Activado**.

4. En la lista desplegable **Acción por realizar cuando no se autentique la solicitud**, seleccione **Iniciar sesión con Azure Active Directory**.

	Esta opción garantiza que ninguna solicitud no autenticada llegará a la aplicación de API.

5. En **Proveedores de autenticación**, haga clic en **Azure Active Directory**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. En la hoja **Configuración de Azure Active Directory**, haga clic en **Rápida**

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	Con la opción **Express**, el Servicio de aplicaciones puede crear automáticamente una aplicación de Azure AD en su [inquilino](https://msdn.microsoft.com/es-ES/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) de Azure AD.

	No es preciso que cree un inquilino, porque todas las cuenta de Azure tienen uno automáticamente.

7. En **Modo de administración**, haga clic en **Crear nueva aplicación de AD**.

	El portal conecta el cuadro de entrada **Crear aplicación** con un valor predeterminado.
	
	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings2.png)

8. Anote el valor del cuadro de entrada **Crear aplicación**; consultará esta aplicación AAD más adelante en el Portal de Azure clásico.

	Azure creará automáticamente una aplicación de Azure AD en su inquilino de Azure AD. De forma predeterminada, la aplicación de Azure AD recibe el mismo nombre que la aplicación de API. Si lo prefiere, escriba un nombre diferente.
 
7. Haga clic en **Aceptar**.

7. En la hoja **Autenticación/autorización**, haga clic en **Guardar**.

Ahora los usuarios de su inquilino de Azure AD son los únicos que pueden llamar a la aplicación de API.

### Opcional: prueba de la aplicación de API

1. En un explorador, vaya a la dirección URL de la aplicación de API: en la hoja **Aplicación de API** del Portal de Azure, haga clic en el vínculo en **URL**.  

	Se le redirigirá a una pantalla de inicio de sesión porque no se permite que las solicitudes no autenticadas tengan acceso a la aplicación de API.

	Si el explorador va a la página que indica que "se creó correctamente", es posible que el explorador ya haya iniciado sesión (en ese caso, abra una ventana de InPrivate o Incognito y vaya a la dirección URL de la aplicación de API).

2. Inicie sesión con las credenciales de un usuario en su inquilino de Azure AD.

	Una vez iniciada la sesión, en el explorador aparece una página que indica que "se creó correctamente".

9. Cierre el explorador.

### Configuración de la aplicación de Azure AD

Cuando se configuró la autenticación de Azure AD, el Servicio de aplicaciones creó automáticamente una aplicación de Azure AD. De forma predeterminada, la nueva aplicación de Azure AD se configuró para proporcionar el token de portador a la dirección URL de la aplicación de API. En esta sección, configurará la aplicación de Azure AD para que proporcione el token de portador al front-end de AngularJS, en lugar de directamente a la aplicación de API de nivel intermedio. El front-end de AngularJS enviará el token a la aplicación de API al llamarla.

11. En el [Portal de Azure clásico](https://manage.windowsazure.com/), vaya a **Azure Active Directory**.

	Tiene que usar al portal clásico porque varias de las opciones de Azure Active Directory a las que necesita acceder aún no están disponibles en el Portal de Azure actual.

12. En la pestaña **Directorio**, haga clic en su inquilino de AAD.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Haga clic en **Aplicaciones > Aplicación propiedad de mi compañía** y después clic en la marca de verificación.

	También es posible que tenga que actualizar la página para ver la nueva aplicación.

15. En la lista de aplicaciones, haga clic en el nombre de aquella que Azure creó automáticamente cuando habilitó la autenticación para la aplicación de API.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Haga clic en **Configurar**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

17. En **URL de inicio de sesión**, escriba la dirección URL de la aplicación web de AngularJS, sin barra diagonal final.

	Por ejemplo: https://todolistangular.azurewebsites.net

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

17. En **URL de respuesta**, escriba la dirección URL de su aplicación web, sin barra diagonal final.

	Por ejemplo: https://todolistsangular.azurewebsites.net

16. Haga clic en **Guardar**.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

15. En la parte inferior de la página, haga clic en **Administrar manifiesto > Descargar manifiesto**.

	![](./media/app-service-api-dotnet-user-principal-auth/downloadmanifest.png)

17. Descargue el archivo en una ubicación en que pueda editar.

16. En el archivo de manifiesto descargado, busque la propiedad `oauth2AllowImplicitFlow`. Cambie el valor de esta propiedad de `false` a `true` y después guarde el archivo.

	Esta configuración es necesaria para el acceso desde una aplicación de una página de JavaScript. Permite que el token de portador de Oauth 2.0 se devuelva al fragmento de dirección URL.

16. Haga clic en **Administrar manifiesto > Cargar manifiesto** y cargue el archivo actualizado en el paso anterior.

17. Copie el valor de **Id. de cliente** y guárdelo en un lugar donde pueda recuperarlo más adelante.

## Configuración del proyecto ToDoListAngular para que use la autenticación

En esta sección, cambiará el front-end de AngularJS para que use la Biblioteca de autenticación de Active Directory (ADAL) para JS para adquirir en Azure AD un token de portador de usuario que ha iniciado sesión. El código incluirá el token en las solicitudes HTTP enviadas al nivel intermedio, tal como se muestra en el diagrama siguiente.

![](./media/app-service-api-dotnet-user-principal-auth/appdiagram.png)

Realice los cambios siguientes en los archivos del proyecto ToDoListAngular.

1. Abra el archivo *index.html*.

2. Quite la marca de comentario de las líneas que hacen referencia a la Biblioteca de autenticación de Active Directory (ADAL) para scripts JS.

		<script src="app/scripts/adal.js"></script>
		<script src="app/scripts/adal-angular.js"></script>

1. Abra el archivo *app/scripts/app.js*.

2. Convierta en comentario el bloque de código marcado para "sin autenticación" y quite la marca de comentario del bloque de código marcado para "con autenticación".

	Este cambio hace referencia al proveedor de autenticación de ADAL JS y le proporciona los valores de configuración. En los pasos siguientes, establezca los valores de configuración tanto de su aplicación de API como de la aplicación de Azure AD.

8. En el código que establece la variable `endpoints`, establezca la dirección URL de la API en la dirección URL de la aplicación de API que creó para el proyecto ToDoListAPI y establezca el identificador de la aplicación de Azure AD en el identificador de cliente que copió del Portal de Azure clásico.

	El código será similar al del ejemplo siguiente:

		var endpoints = {
		    "https://todolistapi0121.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. En la llamada a `adalProvider.init`, establezca `tenant` en el nombre del inquilino y `clientId` en el mismo valor que usó en el paso anterior.

	El código será similar al del ejemplo siguiente:

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: 'contoso.onmicrosoft.com',
		        clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		    },
		    $httpProvider
		    );

	Estos cambios en `app.js` especifican que el código de llamada y la API a la que se ha llamado están en la misma aplicación de Azure AD.

1. Abra el archivo *app/scripts/homeCtrl.js*.

2. Convierta en comentario el bloque de código marcado para "sin autenticación" y quite la marca de comentario del bloque de código marcado para "con autenticación".

1. Abra el archivo *app/scripts/indexCtrl.js*.

2. Convierta en comentario el bloque de código marcado para "sin autenticación" y quite la marca de comentario del bloque de código marcado para "con autenticación".

### Implementación del proyecto ToDoListAngular en Azure

8. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto ToDoListAngular y, a continuación, haga clic en **Publicar**.

9. Haga clic en **Publicar**.

	Visual Studio implementa el proyecto y abre un explorador en la dirección URL base de la aplicación web.

	Para poder probar la aplicación, será preciso que previamente realice un cambio en la aplicación de API de nivel intermedio.

10. Cierre el explorador.

## Configuración del proyecto ToDoListAPI para que use la autenticación

Actualmente, el proyecto ToDoListAPI envía "*" como valor de `owner` a ToDoListDataAPI. En esta sección cambiará el código para enviar el identificador del usuario que ha iniciado sesión.

Realice los cambios siguientes en el proyecto ToDoListAPI.

1. Abra el archivo *Controllers/ToDoListController.cs* y quite la marca de comentario de la línea en cada método de acción que establece `owner` en el valor de la notificación `NameIdentifier` de Azure AD. Por ejemplo:

		owner = ((ClaimsIdentity)User.Identity).FindFirst(ClaimTypes.NameIdentifier).Value;

### Implementación del proyecto ToDoListAPI en Azure

8. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto ToDoListAPI y, luego, haga clic en **Publicar**.

9. Haga clic en **Publicar**.

	Visual Studio implementa el proyecto y abre un explorador en la URL base de la aplicación de API.

10. Cierre el explorador.

### Prueba de la aplicación

9. Vaya a la dirección URL de la aplicación web, **mediante HTTPS, no HTTP**.

8. Haga clic en la pestaña **Lista de tareas pendientes**.

	Se le pedirá que inicie sesión.

9. Inicie sesión con las credenciales de usuario en su inquilino de AAD.

10. Aparece la página **Lista de tareas pendientes**.

	![](./media/app-service-api-dotnet-user-principal-auth/webappindex.png)

	No se muestran tareas pendientes porque hasta todas han sido para el propietario "*". Ahora el nivel intermedio solicita tareas para el usuario que ha iniciado sesión y aún no se ha creado ninguna.

11. Agregue nuevas tareas pendientes para comprobar que la aplicación funciona.

12. En otra ventana del explorador, vaya a la dirección URL de la interfaz de usuario de Swagger para la aplicación de API de ToDoListDataAPI y haga clic en **ToDoList > Get** (ToDoList > Obtener). Escriba un asterisco en el parámetro `owner` y haga clic en **Try it out** (Probarlo).

	La respuesta muestra que las nuevas tareas pendientes tienen el identificador de usuario real de Azure AD en la propiedad Owner.

	![](./media/app-service-api-dotnet-user-principal-auth/todolistapiauth.png)


## Creación de los proyectos desde cero

Los dos proyectos de Web API se crearon con la plantilla de proyecto **Aplicación de API de Azure** y reemplazando el controlador de los valores predeterminados por un controlador de ToDoList.

Para más información sobre cómo crear una aplicación de una página de AngularJS con un back-end Web API 2, consulte [Hands On Lab: Build a Single Page Application (SPA) with ASP.NET Web API and Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs) (Laboratorio práctico: creación de una aplicación de una sola página con ASP.NET Web API y Angular.js). Para obtener información acerca de cómo agregar código de autenticación de Azure AD, consulte los siguientes recursos:

* [Seguridad de las aplicaciones de una sola página de AngularJS con Azure AD](../active-directory/active-directory-devquickstarts-angular.md).
* [Introducing ADAL JS v1 (Presentación de ADAL JS v1)](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/)

## Solución de problemas

Si la aplicación se ejecuta correctamente sin autenticación y luego no funciona al implementar la autenticación, la mayoría de las veces el problema serán opciones de configuración incorrectas o incoherentes. Para empezar, vuelva a comprobar toda la configuración del Servicio de aplicaciones de Azure y Azure Active Directory. Estas son algunas sugerencias específicas:

* En la pestaña **Configurar** de Azure AD, compruebe **URL de respuesta**.
* En Azure AD, descargue el manifiesto y asegúrese de que `oauth2AllowImplicitFlow` se ha cambiado correctamente a `true`. 
* En el código fuente de AngularJS, compruebe la URL de la aplicación de API de nivel intermedio y el identificador de cliente de Azure AD.
* Después de configurar el código en un proyecto, asegúrese de volver a implementar ese proyecto y no uno de los otros.
* Asegúrese de que va a direcciones URL HTTPS en el explorador, no a direcciones URL HTTP.
* Asegúrese de que CORS sigue habilitado en la aplicación de API de nivel intermedio, lo que permite llamadas al nivel intermedio de la dirección URL HTTPS del front-end. Si tiene dudas acerca de si el problema está relacionado con CORS, pruebe a usar "*" como dirección URL de origen permitida.
* Para asegurarse de que obtiene tanta información como sea posible en los mensajes de error, establezca el [modo customErrors en Off (desactivado)](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* La pestaña de Consola de Developer Tools a menudo tiene más información sobre los errores, mientras que las solicitudes HTTP se pueden examinar en la pestaña Red.

## Pasos siguientes

En este tutorial ha aprendido a utilizar la autenticación del Servicio de aplicaciones para una aplicación de API y a llamar a la aplicación de API mediante la biblioteca de ADAL JS. En el siguiente tutorial aprenderá a [acceder de forma segura a la aplicación de API en escenarios de servicio a servicio](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_0302_2016-->