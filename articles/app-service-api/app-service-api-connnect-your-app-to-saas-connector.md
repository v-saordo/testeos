<properties 
	pageTitle="Implementación y configuración de una aplicación de API de conector SaaS" 
	description="Obtenga información sobre cómo configurar un conector SaaS que vaya a instalar en su suscripción de Azure desde Azure Marketplace." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
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

# Implementación y configuración de una aplicación de API de conector SaaS en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Información general

En este tutorial se muestra cómo instalar, configurar y probar un [conector de software como servicio (SaaS)](../app-service-logic-what-are-bizTalk-api-apps.md) en el [Servicio de aplicaciones de Azure](/documentation/services/app-service/) para llamarlo mediante programación, por ejemplo, desde una aplicación móvil. Un conector SaaS es una [aplicación de API](app-service-api-apps-why-best-platform.md) que simplifica la interacción con una plataforma SaaS, como Office 365, Salesforce, Facebook y Dropbox. Si en lugar de usar un conector preempaquetado, quiere crear una aplicación personalizada de API de .NET, vea [Conexión a una plataforma de SaaS desde una aplicación de API de ASP.NET](app-service-api-dotnet-connect-to-saas.md).

Por ejemplo, si desea codificar solicitudes HTTP para leer y escribir archivos en la cuenta de Dropbox, el proceso de autenticación para trabajar directamente con Dropbox es complicado. Un conector de Dropbox se encarga del complejo proceso de autenticación para que el usuario pueda centrarse en escribir el código específico para la tarea en cuestión.

> [AZURE.NOTE]Las instrucciones aquí descritas no serán necesarias si desea usar un conector SaaS desde una aplicación lógica. Para obtener información sobre cómo usar conectores de SaaS dentro de las aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) y [Uso de la aplicación OAUTH personalizada en conectores](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=azurelogicapps&announcementId=4af1e4c5-d220-4457-97d8-d08e427ae6c1).
 
Este tutorial usa un conector de DropBox como ejemplo y le guía a través de los pasos siguientes:

* Instalación del conector de Dropbox en un [grupo de recursos](../resource-group-overview.md) en la suscripción de Azure. 
* Configuración del conector de Dropbox para poder conectarlo al servicio de Dropbox. (Para completar este paso, necesita una cuenta de Dropbox).
* Configuración del grupo de recursos para que solo usuarios autenticados puedan tener acceso a las aplicaciones de API incluidas en dicho grupo.
* Prueba para comprobar que tanto la autenticación del usuario como la autenticación de Dropbox funcionan.

Para obtener más información sobre la autenticación en el Servicio de aplicaciones, vea [Autenticación para aplicaciones de API y aplicaciones móviles](../app-service/app-service-authentication-overview.md).

## Instalación del conector de Dropbox

1. Vaya a la página principal del [Portal de vista previa de Azure] y haga clic en **Marketplace**.

	![Marketplace en el portal de vista previa de Azure](./media/app-service-api-connnect-your-app-to-saas-connector/marketplace.png)

2. Busque Dropbox y, a continuación, haga clic en el icono **Conector de Dropbox**.

	![Hacer clic en el conector de Dropbox](./media/app-service-api-connnect-your-app-to-saas-connector/searchdb.png)
 
3. Haga clic en **Crear**.

	![Click Create](./media/app-service-api-connnect-your-app-to-saas-connector/clickcreate.png)
 
5. En la hoja **Conector de Dropbox**, en **Plan del Servicio de aplicaciones**, haga clic en **Crear nuevo**, y, a continuación, en el cuadro **Crear nuevo plan del Servicio de aplicaciones**, escriba DropBoxPlan.

	Para obtener más información sobre los planes del Servicio de aplicaciones, consulte [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. En **Grupo de recursos**, haga clic en **Crear nuevo** y, a continuación, en el cuadro **Crear nuevo grupo de recursos**, escriba DropboxRG.

	Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../resource-group-overview.md).

7. Seleccione el **Nivel de precios** gratuito. (Si no aparece en la lista, haga clic en **Ver todo**. Después de hacer clic en **F1 Gratis**, haga clic en el botón **Seleccionar**).

	Puede usar un nivel de precios de pago, pero no es necesario para este tutorial.
 
11. Elija una **Ubicación** cercana.

9. <a id="gateway"></a>Mantenga el valor predeterminado "DropboxConnector" como **Nombre** del conector y, a continuación, haga clic en **Crear**.

	![Hacer clic en Crear](./media/app-service-api-connnect-your-app-to-saas-connector/createdropbox.png)

	El Servicio de aplicaciones de Azure crea un grupo de recursos y, dentro de este, crea una aplicación de API del conector de Dropbox y una aplicación web de *puerta de enlace*. La función de la puerta de enlace es administrar el acceso a todas las aplicaciones de API del grupo de recursos.

	Para comprobar el progreso de la creación de recursos, haga clic en **Notificaciones** en la página principal del portal de vista previa de Azure.

3. Cuando Azure termine de crear el conector, haga clic en **Examinar > Grupos de recursos > DropboxRG**.
 
	La hoja **Grupo de recursos** de DropboxRG muestra el conector y la puerta de enlace del grupo de recursos.

	![Diagrama del grupo de recursos](./media/app-service-api-connnect-your-app-to-saas-connector/rgdiagram.png)

## Configuración de la cuenta y del conector de Dropbox

Para habilitar el acceso de la API a la cuenta de Dropbox, debe crear una aplicación de Dropbox en el sitio para desarrolladores de Dropbox. Después, copie los valores de identificador de cliente y secreto del cliente de dicha aplicación de Dropbox en el conector de Dropbox y configure el conector para que acepte solamente solicitudes autenticadas.

### <a id="createdbapp"></a>Creación de una aplicación de Dropbox

[AZURE.INCLUDE [app-service-api-create-dropbox-app](../../includes/app-service-api-create-dropbox-app.md)]

### <a id="copysettings"></a>Copia de la configuración de la aplicación de Dropbox en el conector de Dropbox de Azure y viceversa 

[AZURE.INCLUDE [app-service-api-exchange-dropbox-settings](../../includes/app-service-api-exchange-dropbox-settings.md)]

### Configuración del conector de Dropbox para requerir acceso autenticado

El **Nivel de acceso** del conector está establecido de forma predeterminada en **Interno**, lo que significa que solo otras aplicaciones de API y aplicaciones web del mismo grupo de recursos pueden llamarlo. Sin embargo, Dropbox solo permite el acceso a la cuenta de Dropbox a usuarios autenticados, por tanto debe cambiarse la configuración de nivel de acceso para que requiera la autenticación del usuario.

1. Vuelva a la hoja **Configuración** y haga clic en **Configuración de la aplicación**.

2. En la hoja **Configuración de la aplicación**, cambie el **Nivel de acceso** a **Público (autenticado)** y, a continuación, haga clic en **Guardar**.
	
	![Establecer en Público (autenticado)](./media/app-service-api-connnect-your-app-to-saas-connector/pubauth.png)

Ahora ha configurado el conector de Dropbox para que las llamadas salientes puedan tener acceso a la cuenta de Dropbox y las llamadas entrantes tengan que proceder de usuarios autenticados. En la sección siguiente se especifica el proveedor de autenticación que desea utilizar para autenticar a los usuarios.

## Configuración de la puerta de enlace

Como hemos explicado [anteriormente](#gateway), la puerta de enlace es una aplicación web especial que administra el acceso a todas las aplicaciones de API de un grupo de recursos. Si se desea configurar la puerta de enlace para autenticar a los usuarios, se elige un proveedor de autenticación, como Azure Active Directory, Google o Twitter. Para poder llamar correctamente al conector de Dropbox, los usuarios tendrán que autenticarse antes con el proveedor elegido.

- Para realizar esta tarea, vaya a la sección [Configuración de la puerta de enlace](app-service-api-dotnet-add-authentication.md#configure-the-gateway) del tutorial [Protección de una aplicación de API](app-service-api-dotnet-add-authentication.md) y siga las instrucciones que se indican para configurar la puerta de enlace del grupo de recursos DropboxRG.

## Prueba para comprobar la autenticación del usuario y de Dropbox

Después de haber configurado la puerta de enlace del grupo de recursos DropboxRG para usar un proveedor de autenticación, puede probar el conector de Dropbox.

La mayoría de las veces que se usa un conector, se le llama desde el código. También tenemos tutoriales donde se indica cómo realizar esta tarea. Sin embargo, en ciertas ocasiones deseará validar que el conector funciona antes de conectar otras partes de una aplicación. En este tutorial se muestra cómo usar un explorador y una sencilla herramienta de cliente REST para comprobar que puede interactuarse con el servicio Dropbox a través del conector de Dropbox que acaba de instalar y configurar.

Las instrucciones siguientes muestran cómo realizar estos pasos con las herramientas para desarrolladores del explorador Chrome y la herramienta de cliente REST de Postman. Esto es solo un ejemplo, puede realizar los mismos procedimientos con otros exploradores y herramientas. "Advanced REST Client" (Cliente REST avanzado) es otro complemento de Chrome que se puede usar.

### Inicie sesión como usuario final

Realice los pasos siguientes en una nueva ventana del explorador. Dependiendo del proveedor de autenticación que se use, puede que sea necesario usar una ventana privada o de incógnito.

2. Vaya a la dirección URL de inicio de sesión de la puerta de enlace y el proveedor de autenticación que haya configurado. La URL sigue este patrón: 

    	http://[gatewayurl]/login/[providername]

	La URL de la puerta de enlace puede obtenerse en la hoja **Puerta de enlace** del [Portal del vista previa de Azure]. (Para obtener acceso a la hoja **Puerta de enlace**, haga clic en la puerta de enlace del diagrama que aparece en la hoja **Grupo de recursos**).

	![Dirección URL de la puerta de enlace](./media/app-service-api-connnect-your-app-to-saas-connector/gatewayurl.png)

	El valor [providername] es "facebook" para Facebook, "twitter" para Twitter, "aad" para Azure Active Directory, etc.

	A continuación se muestra una URL de inicio de sesión de ejemplo para Azure Active Directory:

		https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/login/aad/

3. Escriba sus credenciales cuando el explorador muestra una página de inicio de sesión.
 
	Si configuró el inicio de sesión de Azure Active Directory, inicie sesión como uno de los usuarios enumerados en la pestaña **Usuarios** para la aplicación que ha creado en la pestaña Azure Active Directory del [portal de Azure], como admin@contoso.onmicrosoft.com.

	Si la sesión se inicia correctamente, aparece una página en la que se indica "Login complete" (Inicio de sesión completado).

	![](./media/app-service-api-connnect-your-app-to-saas-connector/logindone.png)

### Proporcione la identidad del usuario a Dropbox

Si desea obtener la autorización de Dropbox para usar su API, debe proporcionar las credenciales del usuario a Dropbox. Azure iniciará automáticamente ese proceso, pero para desencadenar el proceso tiene que ir a una dirección URL de puerta de enlace especial en el explorador. Para obtener esa dirección URL, se realiza una solicitud HTTP Post a la puerta de enlace.

La solicitud HTTP Post a la puerta de enlace debe incluir el token de autenticación proporcionado por Azure al iniciar sesión. Las solicitudes del explorador incluyen el token de forma automática porque este se almacena en una cookie pero, en el caso de una solicitud HTTP Post con una herramienta de cliente REST, el token debe obtenerse a partir de la cookie y colocarse en el encabezado de la solicitud HTTP Post.

1. En la ventana del explorador con el mensaje "Login complete" (Inicio de sesión completado), vaya a las herramientas para desarrolladores del explorador y busque la cookie `x-zumo-auth`. Mantenga esta ventana abierta para poder copiar el valor de la cookie en el paso siguiente.
 
	Para obtener el valor de la cookie en Chrome, realice los pasos siguientes:

	- Presione F12 para abrir las herramientas para desarrolladores.
	- Vaya a la pestaña **Resources** (Recursos).
	- Busque las cookies del sitio de la puerta de enlace y haga clic tres veces en **Value** (Valor) de la cookie `x-zumo-auth` para seleccionarlo todo. (Asegúrese de obtener el valor completo de la cookie. Si hace doble clic, puede que solo obtenga la primera parte de esta).  

	![Copiar token](./media/app-service-api-connnect-your-app-to-saas-connector/copytoken.png)

4. En una nueva ventana o pestaña del explorador, cree una solicitud HTTP Post y envíela a la puerta de enlace para solicitar una dirección URL de consentimiento. Incluya el token `x-zumo-auth` como encabezado HTTP.

	La URL sigue este patrón:

		[gatewayurl]/api/consent/list?api-version=2015-01-14&redirecturl=[a URL you want the browser to go to after you authenticate]

	Por ejemplo:

		https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/api/consent/list?api-version=2015-01-14&redirecturl=https://portal.azure.com

	Para enviar la solicitud en Postman en Chrome, realice los pasos siguientes:

	- Escriba la **Request URL** (URL de solicitud).
	- Establezca el método en **Post**.
	- Agregue un encabezado denominado `x-zumo-auth`.
	- En el campo **Value** (Valor) del encabezado, pegue el valor que copió de la cookie `x-zumo-auth`.
	- Haga clic en **Send** (Enviar).
	 
	En la ilustración siguiente se muestra la herramienta Postman de Chrome:

	![Enviar para solicitar dirección URL de consentimiento](./media/app-service-api-connnect-your-app-to-saas-connector/sendforconsent.png)

	La respuesta incluye una dirección URL que se debe usar para iniciar el proceso de inicio de sesión del usuario con Dropbox. (Si obtiene una respuesta de error que indica que no se admite el método Get aunque se tenga la lista desplegable de método establecida en **Post**, asegúrese de que la dirección URL de la puerta de enlace es HTTPS y no HTTP).

	![Dirección URL de consentimiento](./media/app-service-api-connnect-your-app-to-saas-connector/getconsenturl.png)

2. Vaya a la dirección URL recibida como respuesta a la solicitud HTTP Post.

	La respuesta a esta dirección URL redirige el explorador al sitio de Dropbox, donde el usuario inicia sesión y otorga consentimiento para que la aplicación tenga acceso a la cuenta del usuario.
	
	Al finalizar el proceso de inicio de sesión y consentimiento, Dropbox redirige el explorador a la dirección URL de redireccionamiento especificada (por ejemplo, el Portal de vista previa de Azure si siguió el ejemplo y usó https://portal.azure.com). Si se llama desde una aplicación web, esta sería la siguiente página que se muestra en la aplicación web. La aplicación debe comprobar la dirección URL, porque si se produjo un error en el proceso de inicio de sesión o consentimiento, la dirección URL de redireccionamiento podría incluir una variable de cadena de consulta `error`.

3. Mantenga abierta esta ventana del explorador, ya que se va a usar en la sección siguiente.

### Llame a la API del conector de Dropbox 

Azure administra ahora tres tokens de autenticación del usuario:

- Uno del proveedor de autenticación configurado para la puerta de enlace, por ejemplo, Azure Active Directory.
- Uno de Dropbox.
- Uno creado por Azure (el token "zumo").

El único que debe usarse al realizar solicitudes HTTP para trabajar con Dropbox es el token zumo. Azure ha asociado en segundo plano ese token con los otros dos y el conector los proporciona en su nombre al realizar llamadas a Dropbox.

En los pasos siguientes se realiza una solicitud Get al conector de Dropbox para ver los archivos de la cuenta de Dropbox. Como se puede usar una ventana del explorador para una solicitud Get y su ventana del explorador ya tiene el token zumo en una cookie, todo lo que tiene que hacer es ir a una dirección URL y volver a obtener los datos de Dropbox.

1. En la ventana del explorador con el portal de vista previa de Azure abierto, vuelva a la hoja **Aplicación de API** del conector de Dropbox. 

2. Copie la dirección URL de la aplicación de API.
 
4. Haga clic en el icono **Definición de API** para ver los métodos de API disponibles en el conector.

	![Hoja Aplicación de API](./media/app-service-api-connnect-your-app-to-saas-connector/apiappblade.png)

	![Definición de API](./media/app-service-api-connnect-your-app-to-saas-connector/apidef.png)

2. En la ventana del explorador que utilizó para autenticarse en la puerta de enlace, llame al método GET que recupera los nombres de archivo de una carpeta. Este es el patrón de dirección URL:

		[connectorurl]/folder/[foldername]
   
3. Por ejemplo, si la dirección URL del conector es https://dropboxrg784237ad3e7.azurewebsites.net y desea ver los archivos de la carpeta blog, la dirección URL sería:

		https://dropboxrg784237ad3e7.azurewebsites.net/folder/blog

	Los distintos exploradores responden a las llamadas de API de diferente forma. Si se usa Chrome, la respuesta será similar al ejemplo siguiente:

	![Obtener respuesta a una solicitud de carpeta](./media/app-service-api-connnect-your-app-to-saas-connector/dbresponse.png)

## Pasos siguientes

Hemos visto cómo instalar, configurar y probar un conector SaaS. Para obtener más información, vea estos recursos:

* [Uso de conectores](../app-service-logic/app-service-logic-connectors-list.md)
* [Autenticación para aplicaciones de API y aplicaciones móviles](../app-service/app-service-authentication-overview.md)  

[Portal de vista previa de Azure]: https://portal.azure.com/
[Portal del vista previa de Azure]: https://portal.azure.com/
[portal de Azure]: https://manage.windowsazure.com/
 

<!---HONumber=AcomDC_0114_2016-->