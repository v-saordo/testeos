<properties
	pageTitle="Cambiar o actualizar las propiedades de la API de PowerApps en el portal de Azure | Microsoft Azure"
	description="Agregar un icono personalizado, actualizar la directiva XML o actualizar la definición de Swagger de la API de PowerApps"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="guayan"/>

# Actualizar una API existente y sus propiedades

La API que registra en el entorno del servicio de aplicaciones es esencialmente un proxy al servicio back-end. Una vez creada la API, puede cambiar sus propiedades. Por ejemplo, puede:

- Agregar un icono personalizado para la API.
- Cambiar el modo de protección del back-end usado por la API. 
- Actualizar el nombre para mostrar de la API a un nombre más descriptivo.


#### Requisitos previos para empezar

- Suscríbase a [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Cree un [entorno del Servicio de aplicaciones](powerapps-get-started-azure-portal.md).
- Registrar una [API](powerapps-register-from-available-apis) en su entorno.

## Agregar un icono personalizado o agregar un nombre para mostrar descriptivo

1. En el [Portal de Azure](https://portal.azure.com), abra la hoja de la API.
2. Seleccione **Toda la configuración**.
3. En **Configuración**, seleccione **General**: ![][11]

En General, puede cambiar la configuración siguiente:

Configuración | Descripción
--- | ---
Nombre para mostrar | Este nombre se usa al enumerar las *Conexiones disponibles* en PowerApps. De forma predeterminada, usa el nombre del recurso de la API, que es posible que no sea el mejor nombre para los usuarios de PowerApps. Puede escribir un nombre para mostrar descriptivo. Por ejemplo, puede denominarlo **Nuevos pedidos de cliente** o **Ver historial de ventas**.  
URL de icono | Puede agregar un icono personalizado para la API. El icono se usa al enumerar las *Conexiones disponibles* y *Mis conexiones* en PowerApps. De forma predeterminada, se usa el siguiente icono: <br/><br/>![][12] <br/><br/>Cuando se usa un icono personalizado:<br/><ul><li>la dirección URL del icono deben ser de acceso público.</li><li>Puede ser un archivo .png o .svg. Cuando se usa un archivo png, es preferible que sea de 40 x 40 píxeles.</li></ul>
Esquema de dirección URL | Elija el esquema o esquemas que desee que admita la API. Puede elegir **HTTP**, **HTTPS** o **HTTP y HTTPS**. De forma predeterminada, se habilitan HTTP y HTTPS. <br/><br/>El entorno del Servicio de aplicaciones configura automáticamente el esquema según la configuración del back-end. Por lo tanto, si hay algo más que necesita configurar, puede desarrollar o cambiar su servicio back-end. 
Autenticar con el servicio back-end | Después de registrar su servicio back-end en el entorno del Servicio de aplicaciones, es aconsejable proteger el back-end para que los clientes solo lo llamen mediante la API. En función de dónde se implemente el back-end, estarán disponibles las siguientes opciones:<br/><br/><ul><li><strong>Solo accesible a través de esta API</strong>: esta opción solo está disponible cuando el back-end se implementa en el entorno del Servicio de aplicaciones. Una vez seleccionada, esta opción deshabilita cualquier nombre de host del back-end. Dado que el proxy de la API también se ejecuta en el mismo entorno de Servicio de aplicaciones, todavía puede acceder a su back-end.</li><li><strong>Autenticación básica HTTP</strong>: esta opción está disponible independientemente de dónde se implemente su back-end. Cuando la seleccione, escriba un nombre de usuario y una contraseña. Cuando el proxy llama a su back-end, usa la autenticación básica de HTTP para pasar el nombre de usuario y la contraseña del encabezado de autorización HTTP. Por último, su servicio back-end debe confirmar (autenticar) el nombre de usuario y la contraseña introducidos.<br/><br/>Para obtener más información acerca de la implementación de la autenticación básica HTTP en la API 2 web ASP.NET, consulte [Filtros de autenticación en la API 2 web ASP.NET](http://www.asp.net/web-api/overview/security/authentication-filters).</li></ul>


## Actualizar el Swagger de la API

1. En el [Portal de Azure](https://portal.azure.com), abra la hoja de la API.
2. Seleccione **Toda la configuración**.
3. En **configuración**, seleccione **Definición de la API**: ![][13]

**Swagger 2.0** es el formato de definición de API compatible. La definición de la API actual está en el editor de JSON insertado. Puede editar en línea o cargar un nuevo archivo JSON. Después de **Guardar** los cambios, los errores se muestran en esta hoja, incluidos los errores con la definición de la API.

- Para obtener más información acerca de Swagger 2.0, consulte el [sitio web oficial de Swagger](http://swagger.io).
- Para obtener más información acerca de cómo obtener Swagger 2.0 al desarrollar su API, consulte:  
	- [Creación de una aplicación de API ASP.NET en el Servicio de aplicaciones de Azure](../app-service-dotnet-create-api-app.md)
	- [Compilación e implementación de una aplicación de API de Java en el Servicio de aplicaciones de Azure](../app-service-api-java-api-app.md)
	- [Compilación e implementación de una aplicación de API de Node.js en el Servicio de aplicaciones de Azure](../app-service-api-nodejs-api-app.md)
	- [Personalización de definiciones de API generadas por Swashbuckle](../app-service-api-dotnet-swashbuckle-customize.md)
- Para obtener más información acerca de las prácticas recomendadas de uso de Swagger 2.0 para PowerApps, consulte [Desarrollar una API para PowerApps](powerapps-develop-api.md).

## Actualizar la directiva XML de la API

1. En el [Portal de Azure](https://portal.azure.com), abra la hoja de la API.
2. Seleccione **Toda la configuración**.
3. En **Configuración**, seleccione **Directiva**: ![][14]

Esta es la misma directiva admitida por [Administración de API de Azure](https://azure.microsoft.com/services/api-management/). La directiva actual está en el editor XML insertado. Puede editarlo en línea o cargar un nuevo archivo XML. Después de **Guardar** los cambios, los errores se muestran en esta hoja, incluidos los errores de la directiva de la API.

[Directivas de administración de API de Azure](../api-management-howto-policies.md) es un buen recurso para obtener más información acerca de la configuración y la descripción de las directivas.


## Resumen y pasos siguientes
Una vez creada la API, puede usar los pasos de este tema para cambiar su configuración e incluso personalizar algunas opciones de configuración.

Estos son algunos temas y recursos relacionados que le permitirán obtener más información acerca de PowerApps.

- [Configurar una API para conectarse a un back-end protegido por AAD](powerapps-configure-apis-aad.md)
- [Desarrollar una API para PowerApps](powerapps-develop-api.md)

[11]: ./media/powerapps-configure-apis/api-settings-general.png
[12]: ./media/powerapps-configure-apis/api-default-icon.png
[13]: ./media/powerapps-configure-apis/api-settings-api-definition.png
[14]: ./media/powerapps-configure-apis/api-settings-policy.png

<!---HONumber=AcomDC_0128_2016-->