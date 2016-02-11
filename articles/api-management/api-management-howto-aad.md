<properties 
	pageTitle="Procedimiento para autorizar a las cuentas de desarrollador para que usen Azure Active Directory en Administración de API de Azure" 
	description="Obtenga información sobre cómo autorizar a los usuarios para que usen Azure Active Directory en Administración de API." 
	services="api-management" 
	documentationCenter="API Management" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="sdanie"/>

# Procedimiento para autorizar a las cuentas de desarrollador para que usen Azure Active Directory en Administración de API de Azure


## Información general
En esta guía se muestra cómo habilitar el acceso al portal para desarrolladores de todos los usuarios en uno o más directorios de Azure Active Directory. También se muestra cómo administrar los grupos de usuarios de Azure Active Directory mediante la adición de grupos externos que contienen los usuarios de un directorio de Azure Active Directory.

>Para completar los pasos descritos en esta guía, debe tener un directorio de Azure Active Directory en el que crear una aplicación.

## Procedimiento para autorizar a las cuentas de desarrollador para que usen Azure Active Directory

Para comenzar, haga clic en **Administrar** en el Portal de Azure clásico para el servicio Administración de API. De este modo, se abre el portal del publicador de Administración de API.

![Portal del publicador][api-management-management-console]

>Si todavía no ha creado una instancia del servicio Administración de API, consulte [Creación de una instancia del servicio de Administración de API][] en el tutorial [Introducción a la Administración de API de Azure][].

Haga clic en **Seguridad** en el menú **Administración de API** de la izquierda y, después, en **Identidades externas**.

![Identidades externas][api-management-security-external-identities]

Haga clic en **Azure Active Directory**. Anote la **URL de redireccionamiento** y cambie a Azure Active Directory en el Portal de Azure clásico.

![Identidades externas][api-management-security-aad-new]

Haga clic en el botón **Agregar** para crear una nueva aplicación de Azure Active Directory y, a continuación, elija **Agregar una aplicación que mi organización está desarrollando**.

![Agregar nueva aplicación de Azure Active Directory][api-management-new-aad-application-menu]

Escriba un nombre para la aplicación, seleccione **Aplicación web y/o API web** y haga clic en el botón para ir al paso siguiente.

![Nueva aplicación de Azure Active Directory][api-management-new-aad-application-1]

Para la **URL de inicio de sesión**, copie la **URL de redireccionamiento** de la sección **Azure Active Directory** de la pestaña **Identidades externas** en el portal del publicador y quite el sufijo **-aad** del final de la dirección URL. En este ejemplo, la **URL de inicio de sesión** es `https://aad03.portal.current.int-azure-api.net/signin`.

Para la **URL de id. de aplicación**, especifique el dominio predeterminado o un dominio personalizado para Azure Active Directory y anexe a este una cadena única. En este ejemplo, el dominio predeterminado de ****https://contoso5api.onmicrosoft.com** se usa con el sufijo **/api** especificado.

![Propiedades de la nueva aplicación de Azure Active Directory][api-management-new-aad-application-2]

Haga clic en el botón de verificación para guardar y crear la nueva aplicación y cambie a la pestaña **Configurar** para configurar la aplicación.

![Nueva aplicación de Azure Active Directory creada][api-management-new-aad-app-created]

Si se van a usar varios directorios de Azure Active Directory para la aplicación, haga clic en **Sí** para **La aplicación es multiempresa**. El valor predeterminado es **No**.

![La aplicación es multiempresa][api-management-aad-app-multi-tenant]

Copie la **URL de redireccionamiento** de la sección **Azure Active Directory** de la pestaña **Identidades externas** del portal del publicador y péguela en el cuadro de texto **URL de respuesta**.

![URL de respuesta][api-management-aad-reply-url]

Desplácese a la parte inferior de la pestaña Configurar, seleccione la lista desplegable **Permisos de la aplicación** y active **Leer datos de directorio**.

![Permisos de la aplicación][api-management-aad-app-permissions]

Seleccione la lista desplegable **Permisos delegados** y active **Habilitar inicio de sesión y leer perfiles de usuarios**.

![Permisos delegados][api-management-aad-delegated-permissions]

>Para obtener más información acerca de la aplicación y los permisos delegados, vea [Acceder a la API Graph][].

Copie el **Id. de cliente** en el Portapapeles.

![Id. de cliente][api-management-aad-app-client-id]

Vuelva al portal del publicador y pegue el **Id. de cliente** que ha copiado de la configuración de la aplicación de Azure Active Directory.

![Id. de cliente][api-management-client-id]

Vuelva a la configuración de Azure Active Directory, haga clic en la lista desplegable **Seleccionar duración** de la sección **Claves** y especifique un intervalo. En este ejemplo se usa **1 año**.

![Clave][api-management-aad-key-before-save]

Haga clic en **Guardar** para guardar la configuración y mostrar la clave. Copie la clave en el Portapapeles.

>Anote esta clave. Una vez que se cierre la ventana de configuración de Azure Active Directory, la clave no se puede mostrar de nuevo.

![Clave][api-management-aad-key-after-save]

Vuelva al portal del publicador y pegue la clave en el cuadro de texto **Secreto del cliente**.

![Secreto del cliente][api-management-client-secret]

**Inquilinos permitidos** especifica los directorios que tienen acceso a las API de la instancia del servicio Administración de API. Especifique los dominios de las instancias de Azure Active Directory a los que desea conceder acceso. Puede separar varios dominios mediante nuevas líneas, espacios o comas.

![Inquilinos permitidos][api-management-client-allowed-tenants]

En la sección **Inquilinos permitidos** pueden especificarse varios dominios. Para que un usuario pueda iniciar sesión desde otro dominio distinto al dominio original donde se registró la aplicación, un administrador global de ese otro dominio deberá conceder antes a la aplicación permiso de acceso a los datos del directorio. Para conceder permiso, el administrador global debe iniciar sesión en la aplicación y hacer clic en **Aceptar**. En el ejemplo siguiente se ha agregado `miaoaad.onmicrosoft.com` a **Inquilinos permitidos** y un administrador global de dicho dominio inicia sesión por primera vez.

![Permisos][api-management-permissions-form]

>Si un administrador no global intenta iniciar sesión antes de que un administrador global conceda los permisos correspondientes, el inicio de sesión no se puede realizar y aparece una pantalla de error.

Una vez especificada la configuración deseada, haga clic en **Guardar**.

![Save][api-management-client-allowed-tenants-save]

Después de guardar los cambios, los usuarios del directorio de Azure Active Directory especificado pueden iniciar sesión en el portal para desarrolladores mediante los pasos descritos en [Inicio de sesión en el portal para desarrolladores con una cuenta de Azure Active Directory][].

## Adición de un grupo externo de Azure Active Directory

Tras habilitar el acceso para usuarios en un directorio de Azure Active Directory, puede agregar grupos de Azure Active Directory a Administración de API para administrar más fácilmente la asociación de los desarrolladores del grupo a los productos deseados.

> Para configurar un grupo externo de Azure Active Directory, antes debe configurarse el directorio de Azure Active Directory en la pestaña de identidades mediante el procedimiento descrito en la sección anterior.

Los grupos externos de Azure Active Directory se agregan desde la pestaña **Visibilidad** del producto para el que desea conceder acceso al grupo. Haga clic en **Productos** y, a continuación, en el nombre del producto deseado.

![Configure product][api-management-configure-product]

Cambie a la pestaña **Visibilidad** y haga clic en **Agregar grupos de Azure Active Directory**.

![Agregar grupos][api-management-add-groups]

Seleccione el **Inquilino de Azure Active Directory** en la lista desplegable y, a continuación, escriba el nombre del grupo que desee en el cuadro de texto **Grupos para agregar**.

![Seleccionar grupo][api-management-select-group]

El nombre del grupo aparecerá en la lista **Grupos** de Azure Active Directory, como se muestra en el ejemplo siguiente.

![Lista de grupos de Azure Active Directory][api-management-aad-groups-list]

Haga clic en **Agregar** para validar el nombre del grupo y agregar el grupo. En este ejemplo se agrega el grupo externo **Contoso 5 Developers**.

![Group added][api-management-aad-group-added]

Haga clic en **Guardar** para guardar la nueva selección de grupo.

Una vez configurado un grupo de Azure Active Directory de un producto, este aparecerá en la pestaña **Visibilidad** y podrá activarse para el resto de productos de la instancia del servicio Administración de API.

Para revisar y configurar las propiedades de los grupos externos una vez que se han agregado, haga clic en el nombre del grupo en la pestaña **Grupos**.

![Administrar grupos][api-management-groups]

Desde aquí puede editar el **Nombre** y la **Descripción** del grupo.

![Editar grupo][api-management-edit-group]

Los usuarios del directorio de Azure Active Directory configurado pueden iniciar sesión en el portal para desarrolladores, además de ver y suscribirse a los grupos para los que tienen visibilidad mediante las instrucciones de la sección siguiente.

## Inicio de sesión en el portal para desarrolladores con una cuenta de Azure Active Directory

Para iniciar sesión en el portal para desarrolladores con la cuenta de Azure Active Directory configurada en las secciones anteriores, abra una nueva ventana del explorador mediante la **URL de inicio de sesión** de la configuración de la aplicación de Active Directory y haga clic en **Azure Active Directory**.

![Portal para desarrolladores][api-management-dev-portal-signin]

Escriba las credenciales de uno de los usuarios del directorio de Azure Active Directory y haga clic en **Iniciar sesión**.

![Iniciar sesión][api-management-aad-signin]

En caso de requerirse más información, puede que se le solicite un formulario de registro. Complete el formulario de registro y haga clic en **Suscribirse**.

![Registro][api-management-complete-registration]

Ahora el usuario ha iniciado sesión en el portal para desarrolladores para la instancia del servicio Administración de API.

![Registro completado][api-management-registration-complete]



[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introducción a la Administración de API de Azure]: api-management-get-started.md
[Get started with advanced API configuration]: api-management-get-started-advanced.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Creación de una instancia del servicio de Administración de API]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Acceder a la API Graph]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Inicio de sesión en el portal para desarrolladores con una cuenta de Azure Active Directory]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

<!---HONumber=AcomDC_1210_2015-->