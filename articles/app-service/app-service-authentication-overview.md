<properties 
	pageTitle="Autenticación de aplicaciones móviles y aplicaciones de API en el Servicio de aplicaciones de Azure" 
	description="Aprenda a configurar y usar la autenticación para aplicaciones de API y aplicaciones móviles en el Servicio de aplicaciones de Azure." 
	services="app-service" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Autenticación para aplicaciones de API y aplicaciones móviles en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Información general

En este artículo, se explican las características de autenticación integradas para las [aplicaciones de API](../app-service-api/app-service-api-apps-why-best-platform.md) y las [aplicaciones móviles](../app-service-mobile/app-service-mobile-value-prop-preview.md).

La sección [Pasos siguientes](#next-steps), situada al final de este artículo, proporciona vínculos a documentación con procedimientos relacionados.

## Puerta de enlace del Servicio de aplicaciones de Azure

El Servicio de aplicaciones de Azure ofrece servicios de autenticación integrados que implementan [OAuth 2.0](#oauth) y [OpenID Connect](#oauth). Además, funciona con varios *proveedores de identidades*. Un proveedor de identidades es un servicio externo, con la confianza del Servicio de aplicaciones de Azure, que autentica a los usuarios de su aplicación. El Servicio de aplicaciones es compatible con los proveedores de identidades más populares:

* Azure Active Directory
* Cuenta Microsoft
* Google
* Twitter
* Facebook

### Arquitectura de puerta de enlace

Cualquier grupo de recursos de Azure que contenga aplicaciones de API o aplicaciones móviles incluye una *puerta de enlace*. La puerta de enlace es un recurso de Azure que se ejecuta en una aplicación web y realiza tareas administrativas, incluida la autenticación, para aplicaciones de API y aplicaciones móviles en el grupo de recursos.

La puerta de enlace controla los procedimientos de inicio de sesión, administra los tokens y evita que las llamadas no autenticadas tengan acceso a aplicaciones de API que estén [configuradas para requerir acceso autenticado](../app-service-api/app-service-api-dotnet-add-authentication.md#protect-the-api-app). La puerta de enlace puede controlar el acceso a aplicaciones de API porque todas las solicitudes HTTP entrantes destinadas a aplicaciones de API del grupo de recursos se enrutan a través de la puerta de enlace.

El siguiente diagrama ilustra estas funciones de la puerta de enlace.

![](./media/app-service-authentication-overview/gateway.png)

### Características de la puerta de enlace

Los servicios de autenticación de la puerta de enlace ofrecen varias ventajas con respecto a la ejecución de su propia implementación de OAuth 2.0:

* Microsoft proporciona SDK que le permiten realizar tareas de autenticación y autorización mediante una sintaxis simplificada.

* Dado que el Servicio de aplicaciones controla más tareas de autenticación, se minimiza el tiempo de pruebas y desarrollo, a la vez que se evita la mayoría de los efectos (o todos ellos) de los cambios en las implementaciones del proveedor de OAuth.

* Si tiene varias aplicaciones que proteger y las guarda en un grupo de recursos, solo necesita un identificador y un secreto de cliente para cada proveedor de autenticación, porque hay solo una URL de redireccionamiento para la puerta de enlace.

* Los procesos de supervisión y solución de problemas son más fáciles, ya que puede supervisar el tráfico relacionado con la autenticación para un grupo de recursos completo mediante la supervisión de la puerta de enlace.

* La depuración también es más fácil porque puede configurar un programa para que use la puerta de enlace mientras se ejecuta en modo de depuración localmente. Así no tiene que cambiar las URL de redirección en la cuenta del proveedor de identidades.

## Flujo de servidor frente a flujo de cliente

La puerta de enlace del Servicio de aplicaciones ofrece dos modos de autenticar a los clientes: *flujo de cliente* y *flujo de servidor*. En ambos flujos, la aplicación cliente envía las credenciales del usuario (normalmente, el nombre y la contraseña) directamente al proveedor de identidades. Ni la puerta de enlace ni la aplicación reciben las credenciales del usuario en ningún flujo.

### Flujo de cliente

El flujo de cliente significa que la aplicación cliente se comunica directamente con el proveedor de identidades para obtener el token de acceso del proveedor. La aplicación cliente envía el token de acceso del proveedor a la puerta de enlace. La puerta de enlace crea un token de contexto de usuario y lo envía al cliente. Este token de contexto de usuario también es conocido como un "token de Zumo", a causa del nombre del código original de los [Servicios móviles de Azure](/documentation/services/mobile-services/). (Los servicios de autenticación para las aplicaciones de API y las aplicaciones móviles se basan en la misma arquitectura que se desarrolló originalmente para los Servicios móviles).

En el siguiente diagrama, se ilustra este flujo:

![](./media/app-service-authentication-overview/clientflow.png)

El cliente, a continuación, proporciona el token de Zumo en las solicitudes HTTP cuando se llama a aplicaciones móviles o aplicaciones de API protegidas. La puerta de enlace almacena los tokens del proveedor y realiza un seguimiento de cuáles de ellos están asociados a qué tokens de Zumo.

### Flujo de servidor

En el flujo de servidor, la aplicación cliente utiliza la puerta de enlace para comunicarse con el proveedor de identidades para iniciar sesión. El explorador del cliente se dirige a una dirección URL de la puerta de enlace y la puerta de enlace redirige la solicitud a la página de inicio de sesión del proveedor de identidades. Después de que el usuario inicie sesión, la puerta de enlace obtiene el token del proveedor de identidades, crea el token de Zumo y lo envía al cliente.

![](./media/app-service-authentication-overview/serverflow.png)

La próxima interacción entre la aplicación cliente y las aplicaciones de API o las aplicaciones móviles protegidas se controla de la misma forma que en el flujo de cliente: el cliente proporciona el token de Zumo en las solicitudes HTTP.

### Cómo elegir entre el flujo de cliente y de servidor

El flujo de cliente suele ser la mejor opción si el proveedor de identidades que desea utilizar tiene un SDK para la plataforma de cliente a la que desea ofrecer soporte. El flujo de cliente proporciona la mejor experiencia de usuario porque reduce el número de veces que el usuario tiene que escribir las credenciales. Por ejemplo, si el usuario tiene un dispositivo Android, es probable que tenga una cuenta de Google asociada al dispositivo, por lo que si esa cuenta se puede utilizar sin tener que volver a escribir el nombre de usuario y la contraseña, esto mejorará la experiencia del usuario. Ocurre lo mismo con una cuenta de Facebook en un dispositivo Android, iOS o Windows Phone, o con una cuenta de Microsoft en un equipo de escritorio de Windows o un Windows Phone.

En algunos casos, como en los siguientes, el flujo de servidor puede ser una opción mejor:

- No hay ningún SDK cliente nativo para la plataforma de cliente y el proveedor de identidades a los que desea proporcionar soporte.

- Desea poner rápidamente algo en producción; ya mejorará la experiencia del usuario más adelante cuando sea posible. El flujo de servidor permite al Servicio de aplicaciones de Azure realizar más trabajo de autenticación, lo que reduce la cantidad de trabajo de desarrollo y pruebas que tiene que hacer.

## Llamadas salientes en nombre de alguien a las plataformas SaaS

Puede escribir código para realizar llamadas salientes a plataformas de software como servicio (SaaS) en nombre de un usuario que haya iniciado sesión o puede usar una [aplicación de API de conector](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md). Por ejemplo, para publicar un tweet desde la cuenta de Twitter de un usuario, puede usar un [SDK de Twitter](https://dev.twitter.com/overview/api/twitter-libraries) o proporcionar un [conector de Twitter](../app-service-mobile/app-service-logic-connector-twitter.md) en su suscripción de Azure y realizar una llamada a este. Esta sección trata sobre el acceso a una plataforma de SaaS desde el código que se ejecuta en una aplicación de API o una aplicación móvil.

### <a id="obotoidprovider"></a> Uso del token del proveedor de identidades 

La puerta de enlace mantiene un *almacén de tokens* donde asocia un token de Zumo a uno o varios tokens de acceso del proveedor de identidades y tokens de actualización. Cuando se recibe una solicitud HTTP con un token de Zumo válido, la puerta de enlace sabe qué tokens del proveedor de identidades pertenecen a ese usuario.
  
Cuando el código que se ejecuta en su aplicación de API o aplicación móvil debe realizar una llamada a un recurso protegido en nombre del usuario que ha iniciado sesión, se puede recuperar y usar el token del proveedor de identidades del almacén de tokens de la puerta de enlace, como se muestra en el diagrama siguiente. En el diagrama se supone que el cliente ya se ha autenticado en la puerta de enlace y tiene el token Zumo.

![](./media/app-service-authentication-overview/idprovidertoken.png)

Por ejemplo, suponga que el proveedor de identidades es Azure Active Directory (AAD) y su aplicación de API quiere usar el token de acceso de AAD para llamar a la API Graph de AAD o para solicitar acceso a un sitio de SharePoint al que el usuario tiene permisos para acceder. Puede enviar una solicitud a la puerta de enlace para recuperar el token de AAD y luego usar dicho token para llamar a la API Graph o para obtener un token de acceso para el sitio de SharePoint.

### <a id="obotosaas"></a>Obtención del consentimiento del usuario para obtener acceso a otros recursos

La puerta de enlace también tiene características integradas para obtener el consentimiento del usuario cuando se desea acceder a recursos protegidos por un proveedor distinto del proveedor de identidades original. Por ejemplo, para un usuario que inicia sesión con Azure Active Directory, puede que usted desee acceder a los archivos de la cuenta de Dropbox del usuario.

La puerta de enlace del Servicio de aplicaciones incluye compatibilidad integrada para obtener el consentimiento del usuario para este tipo de acceso de los proveedores siguientes:

* Box
* Dropbox
* Facebook
* Google
* Office365
* OneDrive
* QuickBooks
* Salesforce
* SharePointOnline
* Twitter
* Yammer
* Azure Active Directory
* Cuenta Microsoft

Para estos proveedores, la puerta de enlace mantiene tokens de acceso y los asocia con el token de Zumo, igual que hace con el token de acceso del proveedor de identidades. El proceso de obtener el consentimiento del usuario y llamar a una plataforma de SaaS se ilustra en el diagrama siguiente. En el diagrama se supone que el cliente ya se ha autenticado en la puerta de enlace y tiene el token Zumo.

![](./media/app-service-authentication-overview/saastoken.png)

La compatibilidad en tiempo de ejecución del Servicio de aplicaciones y el SDK facilitan relativamente la escritura del código que permite acceder a los recursos protegidos mediante uno de estos proveedores. Hay un paso preliminar que no se muestra en el diagrama: hay que especificar una cuenta con el proveedor y configurar el ID y el secreto del cliente del proveedor en el Servicio de aplicaciones de Azure.

Para estos y muchos otros proveedores, también se puede acceder a recursos protegidos utilizando una [aplicación de API de conector](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md) preempaquetada.

## Disponibilidad de SDK

En el caso de las aplicaciones de API, el SDK para .NET proporciona funcionalidad de autenticación:

* [Microsoft.Azure.AppService](http://www.nuget.org/packages/Microsoft.Azure.AppService): para su uso en un cliente de aplicación de API.  
* [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/): para su uso en un proyecto de API web que se ejecute en una aplicación de API.
 
Además, Visual Studio puede generar automáticamente código que funciona con el SDK para .NET a fin de simplificar aún más el código que se debe escribir para llamar a la aplicación de API. Para obtener más información, consulte [Uso de una aplicación de API del Servicio de aplicaciones de Azure desde un cliente .NET](../app-service-api/app-service-api-dotnet-consume.md).

En el caso de las aplicaciones móviles, los SDK están disponibles para las siguientes plataformas:

- [.NET Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) 
- [iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-preview.md)
- [Xamarin iOS](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md)
- [Xamarin Android](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md)
- [Windows](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md)
- JavaScript (tutorial en desarrollo)

## Métodos de autenticación alternativos

Si los servicios de autenticación de la puerta de enlace no satisfacen las necesidades de su aplicación, puede controlar la autenticación personalmente o utilizar el Servicio de administración de API de Azure.

### <a id="doityourself"></a>Autenticación que controla usted

Puede ejecutar un entorno de autenticación, como [ASP.NET Identity](http://www.asp.net/identity) o [Thinktecture](http://www.thinktecture.com/identityAndAccessControl) en Azure. Esto le permite controlar cómo funciona todo, pero también tendrá que dedicar más tiempo a desarrollar y probar la funcionalidad de autenticación. Además, si tiene varias aplicaciones que proteger con varias URL de redirección, tendrá que configurar varios identificadores y secretos de cliente con los proveedores de autenticación de terceros, como Facebook, Google y Twitter.

En este momento, el Servicio de aplicaciones no admite el uso de una solución que pueda implementar usted mismo junto con la autenticación de puerta de enlace, a diferencia de los [Servicios móviles](mobile-services-dotnet-backend-get-started-custom-authentication.md), donde sí es posible.

### <a id="apim"></a>Administración de API de Azure

Si tiene API existentes y desea protegerlas con la autenticación, puede hacerlo con el Servicio de administración de API de Azure. Para obtener información acerca de cómo utilizar la Administración de API con las aplicaciones de API, consulte esta publicación de blog de Panos Kefalidis sobre cómo [aprovechar el Servicio de administración de API para las aplicaciones de API](http://www.kefalidis.me/2015/06/taking-advantage-of-api-management-for-api-apps/).

## Pasos siguientes

Este artículo, se explican los servicios de autenticación que ofrece el Servicio de aplicaciones de Azure para las aplicaciones de API y las aplicaciones móviles. Estos son algunos vínculos a recursos para obtener información acerca de los protocolos de autenticación subyacentes y documentación sobre cómo usar las características de autenticación del Servicio de aplicaciones.

* [OAuth 2.0, OpenID Connect y JSON Web Tokens (JWT)](#oauth)
* Recursos para aplicaciones de API
	* [Flujo de cliente para aplicaciones de API](#apiaclient)
	* [Flujo de servidor para aplicaciones de API](#apiaserver)
	* [Llamadas en nombre de un usuario a aplicaciones de API](#apiaobo)
* Recursos para aplicaciones móviles
	* [Flujo de cliente para aplicaciones móviles](#maclient)
	* [Flujo de servidor para aplicaciones móviles](#maserver)
	* [Llamadas en nombre de un usuario a aplicaciones móviles](#maobo)

### <a id="oauth"></a>OAuth 2.0, OpenID Connect y JSON Web Tokens (JWT)

* [Introducción a OAuth 2.0](http://shop.oreilly.com/product/0636920021810.do "Introducción a OAuth 2.0") 
* [Introducción a OAuth2, OpenID Connect y JSON Web Tokens (JWT): curso de PluralSight](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [Cómo compilar una API de RESTful API y garantizar su seguridad para varios clientes en ASP.NET: curso de PluralSight](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

### <a id="apiaclient"></a>Flujo de cliente para aplicaciones de API

* [Protección de una aplicación de API](../app-service-api/app-service-api-dotnet-add-authentication.md): la parte de la configuración de la aplicación de API se aplica tanto al flujo de cliente como al de servidor, pero la parte del explorador en pruebas se refiere al flujo de servidor.
* [Uso de una aplicación de API del Servicio de aplicaciones de Azure desde un cliente .NET](../app-service-api/app-service-api-dotnet-consume.md): la aplicación de ejemplo para una llamada autenticada ilustra el flujo de servidor, pero va seguida de una sección [flujo de cliente](../app-service-api/app-service-api-dotnet-consume.md#client-flow) con código de ejemplo.

### <a id="apiaserver"></a>Flujo de servidor para aplicaciones de API

* [Protección de una aplicación de API](../app-service-api/app-service-api-dotnet-add-authentication.md): la parte de la configuración de la aplicación de API se aplica tanto al flujo de cliente como al de servidor, y la parte del explorador en pruebas se refiere al flujo de servidor.
* [Consumo de una aplicación de API en el Servicio de aplicaciones de Azure desde un cliente .NET](../app-service-api/app-service-api-dotnet-consume.md): el código de muestra para una llamada autenticada ilustra el flujo de servidor. 

### <a id="apiaobo"></a>Llamadas en nombre de un usuario a aplicaciones de API

* [Implementación y configuración de una aplicación de API de conector de SaaS en el Servicio de aplicaciones de Azure](../app-service-api/app-service-api-connnect-your-app-to-saas-connector.md): muestra cómo aprovisionar una aplicación de API de conector preempaquetada, cómo configurarla y llamarla utilizando las herramientas del explorador.
* [Conexión a una plataforma de SaaS desde una aplicación de API ASP.NET en el Servicio de aplicaciones de Azure](../app-service-api/app-service-api-dotnet-connect-to-saas.md): muestra cómo escribir su propio conector, es decir, aprovisionar, configurar y escribir código para una aplicación de API personalizada que realiza llamadas en nombre de un usuario a un proveedor de SaaS.

### <a id="maclient"></a>Flujo de cliente para aplicaciones móviles

* [Incorporación del inicio de sesión único de Azure Active Directory a la aplicación iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-aad-sso-preview.md)

### <a id="maserver"></a>Flujo de servidor para aplicaciones móviles

* [Incorporación de la autenticación a la aplicación iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-users-preview.md)
* [Adición de la autenticación a la aplicación Xamarin.iOS](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md)
* [Adición de la autenticación a la aplicación Xamarin.Android](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-users-preview.md)
* [Incorporación de la autenticación a la aplicación de Windows](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md)

### <a id="maobo"></a>Llamadas de aplicaciones móviles en nombre de un usuario a recursos protegidos

* [Obtención de un token de acceso y llamada a la API de SharePoint en una aplicación móvil](../app-service-mobile/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise.md#obtain-token)

<!---HONumber=AcomDC_0204_2016-->