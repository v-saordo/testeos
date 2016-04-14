<properties
	pageTitle="Desarrollar o crear una API hospedada en el entorno del Servicio de aplicaciones de PowerApps Enterprise | Microsoft Azure"
	description="Obtenga información acerca de cómo registrar una API personalizada hospedada en el entorno del Servicio de aplicaciones en el Portal de Azure"
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
   ms.date="12/09/2015"
   ms.author="guayan"/>

# Registrar una API alojada en su entorno del Servicio de aplicaciones
PowerApps admite el registro de API existentes hospedadas en cualquier parte de la nube o localmente, lo cual resulta realmente potente. En algunos escenarios, puede desarrollar o crear algunas nuevas API. Por ejemplo, puede:

- Implementar algunas nuevas funcionalidades para que use su organización.
- Aprovechar funcionalidades o datos existentes para proporcionar una mejor experiencia para los usuarios que crean sus aplicaciones.

Al hospedar las API en su entorno del Servicio de aplicaciones, aprovechará todas las capacidades existentes del [entorno del Servicio de aplicaciones](../app-service-app-service-environment-intro.md) y también obtendrá una mejor experiencia de integración.

Para usar estas API en sus aplicaciones, debe "registrar" las API en el Portal de Azure. Están disponibles las siguientes opciones:

- Registro de una [API administrada por Microsoft o una API administrada por TI](powerapps-register-from-available-apis.md) integrada.
- Registro de una aplicación web, una aplicación de API y una aplicación móvil hospedada en el entorno del Servicio de aplicaciones (en este tema).
- Registro de una de sus propias API de Swagger mediante una [definición de API de Swagger 2.0](powerapps-register-existing-api-from-api-definition.md).

En este artículo se muestra cómo **registrar una aplicación web, una aplicación de API y una aplicación móvil hospedada en el entorno del Servicio de aplicaciones**.

#### Requisitos previos para empezar

- Suscríbase a [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Cree un [entorno del Servicio de aplicaciones](powerapps-get-started-azure-portal.md).


## Desarrollar e implementar una API en su entorno del Servicio de aplicaciones

El desarrollo de una API en el entorno del Servicio de aplicaciones es un proceso sencillo. Elija el lenguaje de programación favorito para crear una API web y, a continuación, use [Swagger 2.0](http://swagger.io) para describir la definición de la API. Estos son algunos ejemplos:

- [Compilación e implementación de una .NET en el Servicio de aplicaciones de Azure](../app-service-api-dotnet-get-started.md)
- [Compilación e implementación de una aplicación de API de Java en el Servicio de aplicaciones de Azure](../app-service-api-java-api-app.md)
- [Compilación e implementación de una aplicación de API de Node.js en el Servicio de aplicaciones de Azure](../app-service-api-nodejs-api-app.md)

También tiene opciones para implementar la API web en un entorno del Servicio de aplicaciones, incluida la implementación desde Visual Studio y la implementación continua con un sistema de control de código fuente. [Implementar una aplicación web en el Servicio de aplicaciones de Azure](../web-sites-deploy.md) es un buen recurso.

## Registrar la API personalizada en el entorno del Servicio de aplicaciones

Después de implementar la API en el entorno del Servicio de aplicaciones, siga estos pasos para efectuar el registro:

1. En el [Portal de Azure](https://portal.azure.com/), seleccione **PowerApps** y, a continuación, **Administrar API**: ![][11]
2. En Administrar API, seleccione **Agregar**: ![][12]  
3. En **Agregar API**, especifique las propiedades de la API:  

	- En **Nombre**, escriba un nombre para la API. Tenga en cuenta que el nombre que escriba se incluirá en la dirección URL de tiempo de ejecución de la API. Elija un nombre descriptivo y único dentro de su organización.	
	- En **Origen**, seleccione **Importar desde las API hospedadas en el entorno del Servicio de aplicaciones**: ![][13]
4. En **API hospedada en el entorno del Servicio de aplicaciones**, seleccione la API que desee importar. Esta lista muestra cada aplicación web, aplicación de API y aplicación móvil de su entorno del servicio de aplicaciones que tiene su propiedad **apiDefinition.url** configurada. Para importar la API, usa la definición de la API Swagger 2.0 expuesta mediante esta propiedad. Asegúrese de que esta dirección URL sea de acceso público al registrar la API: ![][14]
5. Seleccione **AGREGAR** para completar estos pasos.

> [AZURE.TIP]Cuando registre una API, la registrará en su entorno del Servicio de aplicaciones. Una vez se encuentre en el entorno del Servicio de aplicaciones, otras aplicaciones pueden usarla dentro del mismo entorno del Servicio de aplicaciones.

## Resumen y pasos siguientes
En este tema se muestra cómo registrar una API hospedada en su entorno del Servicio de aplicaciones. Estos son algunos temas y recursos relacionados que le permitirán obtener más información sobre PowerApps:

- [Configuración de las propiedades de API](powerapps-configure-apis.md)
- [Dar a los usuarios acceso a las API](powerapps-manage-api-connection-user-access.md)
- [Empezar a crear sus aplicaciones en PowerApps](https://powerapps.microsoft.com/tutorials/)

<!--Reference-->
[11]: ./media/powerapps-register-api-hosted-in-app-service/registered-apis-part.png
[12]: ./media/powerapps-register-api-hosted-in-app-service/add-api-button.png
[13]: ./media/powerapps-register-api-hosted-in-app-service/add-api-blade.png
[14]: ./media/powerapps-register-api-hosted-in-app-service/add-api-select-from-ase.png

<!---HONumber=AcomDC_1210_2015-->