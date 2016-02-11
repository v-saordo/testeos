<properties 
	pageTitle="Configuración de la autenticación de Azure Active Directory para la aplicación de Servicios de aplicaciones" 
	description="Obtenga información acerca de cómo configurar la autenticación de Azure Active Directory para la aplicación de Servicios de aplicaciones" 
	authors="mattchenderson" 
	services="app-service\mobile" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/20/2015" 
	ms.author="mahender"/>

# Configuración de la aplicación del Servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

En este tema se muestra cómo configurar Servicios de aplicaciones de Azure para usar Azure Active Directory como proveedor de autenticación.

> [AZURE.NOTE] En este tema se muestra el uso de la característica Autenticación/autorización del Servicio de aplicaciones. Esto reemplaza a la puerta de enlace del Servicio de aplicaciones en la mayoría de las aplicaciones. Si usa la puerta de enlace, consulte el [método alternativo]. Las diferencias que se aplican al uso de la puerta de enlace se indican con notas a lo largo de esa sección.


## <a name="express"> </a>Configuración de Azure Active Directory mediante la configuración rápida

13. En el [Portal de Azure], vaya a la aplicación. Haga clic en **Configuración** y, a continuación, en **Autenticación/autorización**.

14. Si esta característica no está habilitada, mueva el interruptor a la posición de **activada**.

15. Haga clic en **Azure Active Directory** y luego haga clic en **Rápida** en **Modo de administración**.

16. Haga clic en **Aceptar** para registrar la aplicación en Azure Active Directory. Se creará un nuevo registro. Si, por el contrario, desea elegir un registro existente, haga clic en **Seleccionar una aplicación existente** y luego busque el nombre de un registro creado anteriormente en el inquilino. Haga clic en el registro para seleccionarlo y haga clic en **Aceptar**. A continuación, haga clic en **Aceptar** en la hoja de configuración de Azure Active Directory.

    ![][0]
	
	De forma predeterminada, el Servicio de aplicaciones ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.

17. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante Azure Active Directory, establezca **Acción por realizar cuando no se autentique la solicitud** en **Azure Active Directory**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Azure Active Directory para la autenticación.

17. Haga clic en **Guardar**.

Ahora está preparado para usar Azure Active Directory para realizar la autenticación en la aplicación.

## <a name="advanced"> </a>(Método alternativo) Configuración manual de Azure Active Directory con la configuración avanzada
También puede elegir proporcionar los valores de configuración manualmente. Esta es la solución preferida si el inquilino de AAD que desea usar es diferente de aquel con el que inicia sesión en Azure. Para completar la configuración, primero debe crear un registro en Azure Active Directory y luego proporcionar algunos de los detalles de registro al Servicio de aplicaciones.

### <a name="register"> </a>Registro de la aplicación con Azure Active Directory

1. Inicie sesión en el [Portal de Azure] y vaya a la aplicación. Copie el valor de **Dirección URL**. Se usará para configurar la aplicación de Azure Active Directory.

3. Inicie sesión en el [Portal de Azure clásico] y vaya a **Active Directory**.

    ![][2]

4. Seleccione el directorio y, a continuación, seleccione la pestaña **Aplicaciones** en la parte superior. Haga clic en **AGREGAR** en la parte inferior para crear un nuevo registro de aplicación.

5. Haga clic en **Agregar una aplicación que mi organización está desarrollando**.

6. En el asistente para agregar aplicaciones, escriba un **nombre** para la aplicación y haga clic en el tipo **Aplicación web o API web**. A continuación, haga clic para continuar.

7. En el cuadro **URL DE INICIO DE SESIÓN**, pegue la dirección URL que copió anteriormente. Escriba esa misma dirección URL en el cuadro **URI de id. de aplicación**. A continuación, haga clic para continuar.

8. Una vez que se haya agregado la aplicación, haga clic en la pestaña **Configurar**. Edite la **URL de respuesta** en **Inicio de sesión único** para que sea la URL de la aplicación anexada a la ruta de acceso, _/.auth/login/aad/callback_. Por ejemplo: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Asegúrese de que está utilizando el esquema HTTPS.

    ![][3]
	
	
	> [AZURE.NOTE]
	Si usa la puerta de enlace en lugar de la característica Autenticación/autorización del Servicio de aplicaciones, la URL de respuesta usará en su lugar la URL de la puerta de enlace con la ruta de acceso _/signin-aad_.


9. Haga clic en **Guardar**. A continuación, copie **Id. de cliente** de la aplicación. Más adelante configurará la aplicación para usar este valor.

10. En la barra de comandos, haga clic en **Ver extremos** y luego copie la URL del **documento de metadatos de federación** y descargue ese documento o vaya a él en un explorador.

11. En el elemento raíz **EntityDescriptor**, debe haber un atributo **entityID** del formulario `https://sts.windows.net/` seguido de un GUID específico para el inquilino (denominado "identificador de inquilino"). Copie este valor, actuará como **URL del emisor**. Más adelante configurará la aplicación para usar este valor.

### <a name="secrets"> </a>Incorporación de información de Azure Active Directory a la aplicación

> [AZURE.NOTE]
Si usa la puerta de enlace del Servicio de aplicaciones, omita esta sección y en su lugar, vaya a la puerta de enlace en el portal. Seleccione **Configuración**, **Identidad**, y luego **Azure Active Directory**. Pegue el elemento ClientID y agregue el identificador del inquilino a la lista **Inquilinos permitidos**. Haga clic en **Guardar**.


13. Vuelva al [Portal de Azure] y vaya a la aplicación. Haga clic en **Configuración** y, a continuación, en **Autenticación/autorización**.

14. Si esta característica no está habilitada, mueva el interruptor a la posición de **activada**.

15. Haga clic en **Azure Active Directory** y luego haga clic en **Avanzada** en **Modo de administración**. Pegue el valor de id. de cliente y URL del emisor que obtuvo anteriormente. y, a continuación, haga clic en **Aceptar**.

    ![][1]
	
	De forma predeterminada, el Servicio de aplicaciones ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.

17. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante Azure Active Directory, establezca **Acción por realizar cuando no se autentique la solicitud** en **Azure Active Directory**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Azure Active Directory para la autenticación.

17. Haga clic en **Guardar**.

Ahora está preparado para usar Azure Active Directory para realizar la autenticación en la aplicación.

## (Opcional) Configurar una aplicación de cliente nativo

Azure Active Directory también permite registrar a los clientes nativos, lo que proporciona un mayor control sobre la asignación de permisos. Esto es necesario si desea realizar inicios de sesión con una biblioteca como la **Biblioteca de autenticación de Active Directory**.

1. Vaya a **Active Directory** en el [Portal de Azure clásico].

2. Seleccione el directorio y, a continuación, seleccione la pestaña **Aplicaciones** en la parte superior. Haga clic en **AGREGAR** en la parte inferior para crear un nuevo registro de aplicación.

3. Haga clic en **Agregar una aplicación que mi organización está desarrollando**.

4. En el Asistente para agregar aplicaciones, escriba el **nombre** de la aplicación y haga clic en el tipo **Aplicación de cliente nativo**. A continuación, haga clic para continuar.

5. En el **URI de redireccionamiento** especifique el punto de conexión del sitio _/.auth/login/done_, con el esquema HTTPS. Este valor debería ser similar a \__https://contoso.azurewebsites.net/.auth/login/done_.

6. Una vez que haya agregado la aplicación nativa, haga clic en la pestaña **Configurar**. Busque el **Identificador de cliente** y tome nota de este valor.

7. Desplácese hacia abajo por la página hasta la sección **Permisos para otras aplicaciones** y haga clic en **Agregar aplicación**.

8. Busque la aplicación web que registró y haga clic en el icono del signo de suma. A continuación, haga clic en la marca de verificación para cerrar el cuadro de diálogo. Si la aplicación web no se encuentra, vaya a su registro y agregue una nueva URL de respuesta (por ejemplo, la versión HTTP de la dirección URL actual), haga clic en Guardar y, a continuación, repita estos pasos; la aplicación debería aparecer en la lista.

9. En la entrada nueva que acaba de agregar, abra la lista desplegable **Permisos delegados** y seleccione **Acceso (nombreaplic)**. A continuación, haga clic en **Guardar**.

Ahora ha configurado una aplicación de cliente nativo que puede acceder a la aplicación del Servicio de aplicaciones.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Portal de Azure]: https://portal.azure.com/
[Portal de Azure clásico]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-xamarin-ios-aad-sso.md
[método alternativo]: #advanced

<!---HONumber=AcomDC_0128_2016-->