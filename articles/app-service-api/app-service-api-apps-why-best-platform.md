<properties 
	pageTitle="Información general sobre Aplicaciones de API" 
	description="Descubra por qué el Servicio de aplicaciones de Azure es la mejor plataforma para desarrollar, publicar y hospedar API RESTful." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Información general sobre Aplicaciones de API

Aplicaciones de API son uno de los cuatro tipos que ofrece el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md).

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

El [Servicio de aplicaciones](../app-service/app-service-value-prop-what-is.md) es una plataforma totalmente administrada que ofrece un amplio conjunto de capacidades para escenarios web, móviles y de integración. Las aplicaciones de API del Servicio de aplicaciones ofrecen características que facilitan la compilación, el hospedaje y el consumo de API en la nube y locales. Implemente la API como una aplicación de API en el Servicio de aplicaciones y disfrute de seguridad de nivel empresarial, control de acceso sencillo, conectividad híbrida, generación automática de SDK e integración completa con [Aplicaciones lógicas](../app-service-logic/app-service-logic-what-are-logic-apps.md).

## ¿Por qué las Aplicaciones de API?

Las Aplicaciones de API ofrecen las siguientes características:

- **Fácil consumo**: la compatibilidad integrada con [los metadatos de API de Swagger](#concepts) permite que una gran variedad de clientes pueda consumir las API de forma sencilla. Generan automáticamente código de cliente para las API en diversos lenguajes, como C#, Java y Javascript. Configuran fácilmente [CORS](#concepts) sin cambiar el código. Para más información, consulte [Metadatos de aplicaciones de API del Servicio de aplicaciones para detección de API y generación de código](app-service-api-metadata.md) y [Consumir una aplicación de API de JavaScript mediante CORS](app-service-api-cors-consume-javascript.md). 

- **Control de acceso sencillo**: proteja una aplicación de API frente al acceso no autenticado sin realizar ningún cambio en el código. Los servicios de autenticación integrados protegen las API frente al acceso de otros servicios o de clientes que representan a los usuarios. Los proveedores de identidad admitidos incluyen Azure Active Directory y proveedores de terceros, como Facebook y Twitter. Los clientes pueden usar la biblioteca de autenticación de Active Directory (AAL) o el SDK de Aplicaciones móviles. Para más información, consulte [Expansión de la autorización o autenticación del Servicio de aplicaciones](/blog/announcing-app-service-authentication-authorization/) y [Aplicaciones de API del Servicio de aplicaciones: lo que ha cambiado](app-service-api-whats-changed.md).

- **Integración con visual Studio**: existen herramientas dedicadas en Visual Studio que permiten optimizar las tareas de creación, implementación, consumo, depuración y administración de las aplicaciones de API. Para más información, consulte [Anuncio de la versión 2.8.1 del SDK de Azure para .NET](/blog/announcing-azure-sdk-2-8-1-for-net/).

- **Integración con aplicaciones lógicas**: las [aplicaciones lógicas del Servicio de aplicaciones](../app-service-logic/app-service-logic-what-are-logic-apps.md) pueden consumir las aplicaciones de API que cree. Aprenda a hacerlo en [Uso de la API personalizada hospedada en Servicio de aplicaciones con aplicaciones lógicas](../app-service-logic/app-service-logic-custom-hosted-api.md). Para obtener información sobre los cambios continuos en la manera en que las aplicaciones de API se integran con las aplicaciones lógicas, consulte [Aplicaciones de API del Servicio de aplicaciones: lo que ha cambiado](app-service-api-whats-changed.md).

- **La API existente puede incorporarse tal cual**: no es necesario realizar ningún cambio en el código de las API existentes para aprovechar las características de las aplicaciones de API, solo tiene que implementar el código en una aplicación de API. La API puede usar cualquier lenguaje o marco admitidos por el Servicio de aplicaciones, como ASP.NET y C#, Java, PHP, Node.js y Python.

Además, las características que ofrecen las Aplicaciones de API, las Aplicaciones web y las Aplicaciones móviles son intercambiables. Esto significa que una instancia de Aplicaciones de API puede aprovechar las características de desarrollo web y de dispositivos móviles y de hospedaje que ofrecen las Aplicaciones web y Aplicaciones móviles. Lo contrario también es cierto: por ejemplo, puede usar una aplicación web para hospedar una API y seguir aprovechando los metadatos de Swagger para la generación de código de cliente y CORS para el acceso entre dominios del explorador. Para más información, consulte la [información general sobre Aplicaciones web](../app-service-web/app-service-web-overview.md) y la [información general sobre aplicaciones móviles](../app-service-mobile/app-service-mobile-value-prop.md).

>[AZURE.NOTE]Puede usar [Administración de API de Azure](../api-management/api-management-key-concepts.md) para controlar el acceso de cliente a las API que se hospedan en aplicaciones de API del Servicio de aplicaciones. Aunque las Aplicaciones de API proporcionan servicios de autenticación, hay otras características de administración de acceso que no ofrecen, que sí ofrece la Administración de API, como la consolidación y la limitación de los puntos de conexión.

## <a id="concepts"></a> Conceptos de Aplicaciones de API

- **Swagger**: un marco para la documentación y la detección de una API de RESTful, que se usa de forma predeterminada en las aplicaciones de API. Para obtener más información, consulte [http://swagger.io/](http://swagger.io/).
- **Uso compartido de recursos entre orígenes (CORS)**: un mecanismo que permite a JavaScript ejecutarse en un explorador para realizar llamadas a una API hospedada en un dominio diferente a aquel en el que se cargó la página web. Para más información, consulte [Consumo de una aplicación de API desde JavaScript con CORS](app-service-api-cors-consume-javascript.md). 
- **Desencadenador**: una API de REST que las [aplicaciones lógicas](../app-service-logic/app-service-logic-what-are-logic-apps.md) pueden llamar para iniciar un proceso de flujo de trabajo cuando se cumple una determinada condición. Por ejemplo, una aplicación de API puede proporcionar un método al que la aplicación lógica llame periódicamente para buscar una frase determinada en una fuente de Twitter. Para obtener más información, consulte [Desencadenadores de aplicación de API](app-service-api-dotnet-triggers.md).
- **Acción**: una API de REST a la que las [aplicaciones lógicas](../app-service-logic/app-service-logic-what-are-logic-apps.md) pueden llamar para procesar datos después de que un desencadenador haya iniciado un flujo de trabajo. Por ejemplo, una aplicación de API puede proporcionar un método al que la aplicación lógica llame para responder a un tweet encontrado por el desencadenador de Twitter. Las acciones son métodos de API expuestos por una definición de la API de Swagger.

## Introducción

Para empezar a trabajar con aplicaciones de API, siga el tutorial [Introducción a Aplicaciones de API](app-service-api-dotnet-get-started.md).

Para ver una lista de problemas conocidos con aplicaciones de API, vea [la entrada del foro de problemas conocidos de Aplicaciones de API](https://social.msdn.microsoft.com/Forums/es-ES/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md).

<!---HONumber=AcomDC_0114_2016-->