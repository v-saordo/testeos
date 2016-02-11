<properties
	pageTitle="Aplicaciones de API del Servicio de aplicaciones: lo que ha cambiado | Microsoft Azure"
	description="Consulte las novedades de las aplicaciones de API en el Servicio de aplicaciones de Azure."
	services="app-service\api"
	documentationCenter=".net"
	authors="mohitsriv"
	manager="wpickett"
	editor="tdykstra"/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/13/2016"
	ms.author="mohisri"/>

# Aplicaciones de API del Servicio de aplicaciones: lo que ha cambiado

En el evento Connect() de noviembre de 2015, se [anunciaron](https://azure.microsoft.com/blog/azure-app-service-updates-november-2015/) varias mejoras que se habían realizado en el Servicio de aplicaciones de Azure. Estas mejoras incluyen cambios subyacentes en Aplicaciones de API para equipararlas con las aplicaciones móviles y web, reducir el número de conceptos y mejorar el rendimiento en tiempo de ejecución e implementación. A partir del 30 de noviembre de 2015, las nuevas aplicaciones de API cree con el portal de administración o con las herramientas más recientes de Azure reflejarán esos cambios. Este artículo describe estos cambios y explica cómo volver a implementar las aplicaciones existentes para sacar partido de las funcionalidades.


> [AZURE.NOTE] La versión preliminar inicial de Aplicaciones de API admitía dos escenarios principales: 1) API personalizadas para su uso en aplicaciones lógicas o en sus propios clientes y 2) API de Marketplace (con frecuencia conectores de SaaS) para su uso en aplicaciones lógicas. Este artículo trata el primer escenario, las API personalizadas. Para las API de Marketplace, a principios de 2016 se incorporará mejoras en la experiencia de diseñador de aplicaciones lógicas y en la base de conectividad subyacente. Las API de Marketplace existentes permanecen disponibles en el diseñador de aplicaciones lógicas.

## Cambios de características
Las principales características de Aplicaciones de API (autenticación, CORS y metadatos de API) se movieron directamente al Servicio de aplicaciones. Con este cambio, las características están disponibles a través de aplicaciones web, móviles y de API. De hecho, las tres comparten el mismo el tipo de recurso **Microsoft.Web/Sites** en el Administrador de recursos. La puerta de enlace de Aplicaciones de API ya no es necesario ni se ofrece con Aplicaciones de API. También facilita el uso de Administración de API de Azure ya que habrá solo una puerta de enlace de Administración de API.

![Introducción a las aplicaciones de API](./media/app-service-api-whats-changed/api-apps-overview.png)

Un principio clave del diseño de la actualización de Aplicaciones de API es permitirle traer su API tal cual, en el lenguaje elegido. Si la API ya está implementada como una aplicación web o una aplicación móvil*, no es necesario volver a implementar la aplicación para aprovechar las nuevas características.

> [AZURE.NOTE] *Si actualmente usa la versión preliminar de Aplicaciones de API, a continuación se detallan las instrucciones de migración.

### Autenticación
Las características de autenticación Aplicaciones de API, Servicios móviles o Aplicaciones móviles y Aplicaciones web existentes se unificaron y están disponibles en una sola hoja de autenticación del Servicio de aplicaciones de Azure, en el portal de administración. Para ver una introducción a los servicios de autenticación del Servicio de aplicaciones, consulte [Expanding App Service authentication/authorization](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

En escenarios de API, hay varias funcionalidades nuevas relacionadas:

- **Compatibilidad para usar Azure Active Directory directamente**, sin que el código cliente tenga que intercambiar el token de AAD por un token de sesión: el cliente solo puede incluir los tokens de AAD en el encabezado de autorización, según la especificación de los tokens de portador. Esto significa también que no se necesita ningún SDK específicos del Servicio de aplicaciones en el lado cliente o servidor. 
- **Acceso “interno” o servicio a servicio**: si tiene un proceso de demonio o algún otro cliente que necesitan acceso a las API sin una interfaz, puede solicitar un token mediante una entidad de servicio de AAD y pasarlo al Servicio de aplicaciones para la autenticación con su aplicación.
- **Autorización diferida**: muchas aplicaciones tienen restricciones de acceso diferentes para las distintas partes de la aplicación. Quizás quiera que algunas API esté disponibles públicamente, mientras que otras requieren inicio de sesión. La característica de autenticación y autorización original era todo o nada, y todo el sitio requería inicio de sesión. Esta opción sigue existiendo, pero también puede permitir que el código de aplicación represente las decisiones de acceso después de que el Servicio de aplicaciones autentique al usuario.
 
Para más información sobre la funciones de autenticación nuevas, consulte [Autenticación y autorización para Aplicaciones de API en el Servicio de aplicaciones de Azure](app-service-api-authentication.md). Para obtener información acerca de cómo migrar aplicaciones de desde el modelo anterior de aplicaciones de API al nuevo, consulte [Migración de aplicaciones de API existentes](#migrating-existing-api-apps) más adelante en este mismo artículo.
 
### CORS
En lugar de una configuración de aplicación **MS\_CrossDomainOrigins** delimitada por comas, ahora hay una hoja en el Portal de administración de Azure para configurar CORS. Como alternativa, se puede configurar mediante herramientas del Administrador de recursos, como Azure PowerShell, CLI o el [Explorador de recursos](https://resources.azure.com/). Establezca la propiedad **cors** del tipo de recurso **Microsoft.Web/sites/config** para su recurso **&lt;nombre de sitio&gt;/web**. Por ejemplo:

    {
        "cors": {
            "allowedOrigins": [
                "https://localhost:44300"
            ]
        }
    } 

### Metadatos de API
La hoja de la definición de API ya está disponible en las aplicaciones web, móviles y de API. En el portal de administración, puede especificar una dirección URL relativa o una dirección URL absoluta que apunta a un punto de conexión que hospeda una representación de la API para Swagger 2.0. También se puede configurar mediante las herramientas del Administrador de recursos. Establezca la propiedad **apiDefinition** en el tipo de recurso **Microsoft.Web/sites/config** para su recurso **&lt;nombre de sitio&gt;/web**. Por ejemplo:

    {
        "apiDefinition":
        {
            "url": "https://myStorageAccount.blob.core.windows.net/swagger/apiDefinition.json"
        }
    }

En este momento, es preciso que se pueda acceder públicamente al punto de conexión de metadatos sin autenticación para que muchos clientes de bajada (p.ej., la generación de clientes de la API de REST de Visual Studio y el flujo "Agregar API" de PowerApps) lo consuman. Esto significa que si está usando la autenticación del Servicio de aplicaciones y desea exponer la definición de API desde su propia aplicación, deberá usar la opción de autenticación diferida descrita anteriormente para que la ruta a los metadatos de Swagger sea pública.

## Portal de administración
Al seleccionar **Nuevo > Web y móvil > Aplicación de API** en el portal, se crearán aplicaciones de API que reflejarán las nuevas capacidades que se describen en el artículo. **Examinar > Aplicaciones de API** mostrará solamente las nuevas aplicaciones de API. Una vez que se examina una aplicación de API, la hoja comparte el mismo diseño y funcionalidades que las aplicaciones web y móviles. Las únicas diferencias son el contenido de inicio rápido y el orden de la configuración.

Las aplicaciones de API existentes (o las aplicaciones de API de Marketplace creadas desde aplicaciones lógicas) con las funcionalidades de la versión preliminar anterior seguirán siendo visibles en el diseñador de aplicaciones lógicas y cuando se examinan todos los recursos de un grupo de recursos. Si no necesita crear una aplicación de API con las capacidades de vista previa anteriores, el paquete estará disponible y se podrá buscar en Azure Marketplace como **Web y móvil > Aplicaciones de API (vista previa)**.

## Visual Studio

La mayoría de las herramientas de Aplicaciones web funcionarán con las nuevas aplicaciones de API porque comparten el mismo tipo de recurso subyacente **Microsoft.Web/Sites**. Las herramientas de Visual Studio para Azure, sin embargo, debe actualizarse a la versión 2.8.1 o posterior puesto que expone varias funcionalidades específicas de las API. Descargue el SDK desde la [página de descargas de Azure](https://azure.microsoft.com/downloads/).

Con la racionalización de los tipos del Servicio de aplicaciones, la publicación también está unificada en **Publicar > Servicio de aplicaciones de Microsoft Azure**:

![Publicación de aplicaciones de API](./media/app-service-api-whats-changed/api-apps-publish.png)

Para obtener más información sobre el SDK 2.8.1, lea la [entrada del blog](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/) sobre el anuncio.

Como alternativa, puede importar manualmente el perfil de publicación desde el portal de administración para habilitar la publicación. Sin embargo, Cloud Explorer, la generación de código y la creación y selección de aplicaciones de API requerirán el SDK 2.8.1 o superior.

La capacidad para publicar aplicaciones de API existentes con las funcionalidades de la versión preliminar anterior sigue estando disponible en el SDK 2.8.1. Si ya publicó el proyecto, no es necesaria realizar ninguna otra acción. Para configurar la publicación, elija **Aplicaciones de API (clásico)** desde la lista desplegable **Más opciones** en el cuadro de diálogo de publicación.

## Migración de aplicaciones de API existentes
Si la API personalizada se implementa en la versión preliminar anterior de Aplicaciones de API, le pedimos que migre al nuevo modelo de Aplicaciones de API antes del 31 de diciembre de 2015. Como el modelo nuevo y el antiguo se basan en las API web hospedadas en el Servicio de aplicaciones, se puede reutilizar la mayoría del código existente.

### Hospedaje y reimplementación
Los pasos para la reimplementación son los mismos que para la implementación de cualquier API Web existente en el servicio de aplicaciones. Pasos:

1. Crear una aplicación de API vacía. Esto puede hacerse en el portal con Nuevo > Aplicación de API, en Visual Studio desde publicación o desde las herramientas del Administrador de recursos. Si usa las herramientas o plantillas del Administrador de recursos, establezca el valor de **kind** en **api** en el tipo de recurso **Microsoft.Web/Sites** para que las guías de inicio rápido y la configuración del portal de administración estén orientados a los escenarios de API.
2. Conéctese e implemente el proyecto en la aplicación de API vacía mediante cualquiera de los mecanismos de implementación admitidos por el Servicio de aplicaciones. Para más información, consulte la [documentación de implementación del Servicio de aplicaciones de Azure](../app-service-web/web-sites-deploy.md). 
  
### Autenticación
Los servicios de autenticación del Servicio de aplicaciones admiten las mismas funcionalidades que estaban disponibles con el modelo de aplicaciones de API anterior. Si usa tokens de sesión y necesita SDK, use los siguientes SDK de cliente y servidor:

- Cliente: [SDK de cliente móvil de Azure](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- Servidor: [Extensión de autenticación de .NET para Aplicaciones móviles de Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/) 

Si usaba los SDK alfa del Servicio de aplicaciones, estos están en desuso:

- Cliente: [SDK del Servicio de aplicaciones de Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.AppService)
- Servidor: [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service)

En particular con Azure Active Directory, no se necesita ningún SDK específico del Servicio de aplicaciones si usa el token de AAD directamente.

### Acceso interno
El modelo de aplicaciones de API anterior incluía un nivel de acceso interno integrado. Esto requiere usar el SDK para firmar las solicitudes. Tal y como se describió anteriormente, con el nuevo modelo de Aplicaciones de API se pueden usar entidades de servicio de AAD como alternativa para la autenticación de servicio a servicio sin necesidad de un SDK específico del Servicio de aplicaciones. Puede obtener más información en [Autenticación de entidad de servicio para Aplicaciones de API en el Servicio de aplicaciones de Azure](app-service-api-dotnet-service-principal-auth.md).

### Detección
El modelo de aplicaciones de API anterior tenía API para descubrir otras aplicaciones de API en tiempo de ejecución en el mismo grupo de recursos detrás de la misma puerta de enlace. Esto es especialmente útil en las arquitecturas que implementan patrones de microservicio. Aunque esto no se admite directamente, hay varias opciones disponibles:

1. Usar la API del Administrador de recursos de Azure para la detección.
2. Coloque Administración de API de Azure delante de sus API hospedadas por el Servicio de aplicaciones. Administración de API de Azure sirve como fachada y puede proporcionar una URL con orientación externa estable aunque cambie la topología interna.
3. Crear su propia aplicación de API de detección y hacer que otras aplicaciones de API se registren con la aplicación de detección en el inicio.
4. Durante la implementación, rellene los valores de configuración de todas las aplicaciones de API (y clientes) con los puntos de conexión de las otras aplicaciones de API. Esto es viable en implementaciones de plantilla y gracias a que las aplicaciones de API ahora permiten controlar la dirección URL.

### Aplicaciones lógicas
El diseñador de aplicaciones lógicas incorporará una integración especialmente fluida con el nuevo modelo de Aplicaciones de API a principios de 2016. Dicho esto, el conector HTTP integrado en las aplicaciones lógicas puede invocar cualquier punto de conexión HTTP y admite la autenticación de entidad de servicio, que también se admite de forma nativa en los servicios de autenticación del Servicio de aplicaciones. Aprenda a consumir una API hospedada por el Servicio de aplicaciones en aplicaciones lógicas en [Uso de la API personalizada hospedada en Servicio de aplicaciones con aplicaciones lógicas](../app-service-logic/app-service-logic-custom-hosted-api.md).

### <a id="documentation"></a> Documentación del modelo de aplicaciones de API anterior
Algunos artículos de [azure.microsoft.com](https://azure.microsoft.com/) que se escribieron para el anterior modelo de aplicaciones de API no se aplican al nuevo modelo, por lo que se quitará del sitio. Sus direcciones URL se redirigirán a las equivalentes más cercanas que funcionen con el nuevo modelo, pero podrá ver los artículos anteriores en el [repositorio de documentación de GitHub para azure.microsoft.com](https://github.com/Azure/azure-content). La mayoría de los artículos que probablemente desee se encuentran en la carpeta [artículos /--api del servicio](https://github.com/Azure/azure-content/tree/master/articles/app-service-api). Aquí encontrará vínculos directos a algunos de los que más probablemente sigan siendo útiles si se admiten aplicaciones de API anteriores o se crean nuevas aplicaciones de API del conector desde Marketplace.

* [Información general la autenticación](https://github.com/Azure/azure-content/tree/master/articles/app-service/app-service-authentication-overview.md)
* [Protección de una aplicación de API](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-dotnet-add-authentication.md)
* [Consumo de una aplicación de API interna](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-dotnet-consume-internal.md)
* [Consumo mediante la autenticación de flujo de cliente](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-authentication-client-flow.md)
* [Implementación y configuración de una aplicación de API de conector SaaS](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-connnect-your-app-to-saas-connector.md)
* [Aprovisionamiento de una aplicación de API con una nueva puerta de enlace](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-arm-new-gateway-provision.md)
* [Depuración de una aplicación de API](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-dotnet-debug.md)
* [Conexión a una plataforma de SaaS](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-dotnet-connect-to-saas.md)
* [Mejora de una aplicación de API para aplicaciones lógicas](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-optimize-for-logic-apps.md)
* [Desencadenadores de aplicaciones de API](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-dotnet-triggers.md)

## Pasos siguientes
Para más información, consulte los artículos de la [sección Documentación de Aplicaciones de API](https://azure.microsoft.com/documentation/services/app-service/api/). Se han actualizado para reflejar el nuevo modelo de Aplicaciones de API. Además, visite los foros para obtener más información e instrucciones sobre la migración:

- [Foro de MSDN](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureAPIApps)
- [Desbordamiento de la pila](http://stackoverflow.com/questions/tagged/azure-api-apps)

<!---HONumber=AcomDC_0128_2016-->