<properties
	pageTitle="Seguridad OAUTH en conectores SaaS y aplicaciones de API | Azure"
	description="Obtenga información acerca la seguridad OAUTH en los conectores y las aplicaciones de API del Servicio de aplicaciones de Azure; arquitectura de microservicios; saas"
	services="app-service\logic"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/18/2016"
	ms.author="mandia"/>


# Obtención de información acerca de la seguridad OAUTH en conectores SaaS

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

Muchos de los conectores de software como servicio (SaaS), como Facebook, Twitter, DropBox, etc., requieren que los usuarios se autentiquen mediante el protocolo OAUTH. Cuando utiliza estos conectores de SaaS de Aplicaciones lógicas, proporcionamos una experiencia de usuario simplificada donde hace clic en "Autorizar" en el diseñador de Aplicaciones de lógicas. Cuando haga clic en **Autorizar**, se le pedirá que inicie sesión (si todavía no la ha iniciado) y dé su consentimiento para conectarse al servicio SaaS en su nombre. Cuando da su consentimiento y autorización, las aplicaciones lógicas pueden acceder a estos servicios SaaS.

## Creación de su propia aplicación SaaS
Esta experiencia simplificada es posible porque previamente creamos y registramos nuestra aplicación en estos servicios SaaS. En algunos casos, puede que desee registrar y utilizar su propia aplicación. Esto es necesario, por ejemplo, si desea utilizar estos conectores SaaS en sus aplicaciones personalizadas, por ejemplo en [Implementación de una aplicación de API de conector SaaS](../app-service-api/app-service-api-connnect-your-app-to-saas-connector.md). Este ejemplo utiliza el conector de DropBox, pero el proceso es el mismo para todos los conectores que se basan en OAUTH.

Incluso en el contexto de Aplicaciones lógicas, puede utilizar su propia aplicación en lugar de utilizar la aplicación predeterminada que proporcionamos. Si el botón "Autorizar" no se puede conectar, intente crear su propia aplicación. A continuación se enumeran estos pasos para el conector de Twitter:

1. Abra el conector de Twitter en el portal de vista previa de Azure. Vaya a **Examinar** > **Aplicaciones de API**. Seleccione el conector de Twitter: ![][1]

2. Seleccione **Configuración** > **Autenticación**: ![][2]

3. Copie el valor de **URI de redirección**: ![][3]

4. Vaya a [Twitter](http://apps.twitter.com)y **Crear una nueva aplicación**. En la propiedad **Dirección URL de devolución de llamadas**, pegue el valor de **URI de redirección** copiado del conector de Twitter: ![][4]
5. Cuando la aplicación de Twitter se cree, seleccione **Clave y tokens de acceso**. Copie estos valores.
6. En la configuración de autenticación del conector de Twitter, pegue estos valores en las propiedades **Id. de cliente** y **Secreto del cliente**: ![][5]  
7. Guarde la configuración del conector.  

Ahora, podrá usar el conector de Aplicaciones lógicas. Cuando utilice este conector de Aplicaciones lógicas, este usa su aplicación en lugar de la aplicación predeterminada.

> [AZURE.NOTE] Si ha autorizado una aplicación previamente, tendrá que volver a autorizarla.


<!--Image references-->
[1]: ./media/app-service-logic-oauth-security/TwitterConnector.png
[2]: ./media/app-service-logic-oauth-security/Authentication.png
[3]: ./media/app-service-logic-oauth-security/RedirectURI.png
[4]: ./media/app-service-logic-oauth-security/TwitterApp.png
[5]: ./media/app-service-logic-oauth-security/TwitterKeys.png

<!---HONumber=AcomDC_0224_2016-->