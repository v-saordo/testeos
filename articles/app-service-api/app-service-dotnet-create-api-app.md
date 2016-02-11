<properties
	pageTitle="Creación de una aplicación de API de ASP.NET en Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Obtenga información acerca de cómo crear una aplicación de API de ASP.NET en Servicio de aplicaciones de Azure con Visual Studio 2013."
	services="app-service\api"
	documentationCenter=".net"
	authors="bradygaster"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Creación de una aplicación de API ASP.NET en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Información general

En este tutorial se muestra cómo crear un proyecto de API web ASP.NET que esté configurado para su implementación en la nube como [aplicación de API en el Servicio de aplicaciones de Azure](app-service-api-apps-why-best-platform.md). Para obtener información sobre cómo configurar un proyecto de API web existente para implementarlo como una aplicación de API, consulte [Configuración de un proyecto Web API como una aplicación de API](app-service-dotnet-create-api-app-visual-studio.md).

Se trata de un tutorial rápido y sencillo que solo muestra cómo crear un proyecto de Visual Studio mediante una plantilla. Es el primer tutorial de una serie que también muestra cómo [implementar](app-service-dotnet-deploy-api-app.md) y [depurar](../app-service-dotnet-remotely-debug-api-app.md) el proyecto de aplicación de API creado en este tutorial. Para información más detallada sobre cómo trabajar con las aplicaciones de API, consulte la sección [Pasos siguientes](#next-steps) al final del tutorial.

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

Este tutorial requiere la versión 2.6 o posterior de SDK de Azure para .NET.

## Creación de un proyecto de aplicación de API

Cuando las instrucciones indiquen que escriba un nombre para el proyecto, escriba **ContactsList**.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Pasos siguientes

La aplicación de API ya está lista para implementarse. Para ello, puede seguir el tutorial [Implementación de una aplicación de API](app-service-dotnet-deploy-api-app.md).

Para obtener información sobre cómo usar código de cliente generado automáticamente para llamar a aplicaciones de API, consulte [Consumo de una aplicación de API desde un cliente .NET](app-service-api-dotnet-consume.md).

Para información sobre cómo personalizar los metadatos Swagger generados automáticamente para una aplicación de API, consulte [Personalización de definiciones de API generadas por Swashbuckle](app-service-api-dotnet-swashbuckle-customize.md).

Para información sobre cómo crear, eliminar y configurar aplicaciones de API en el Portal de vista previa de Azure, consulte [Administración de aplicaciones de API](app-service-api-manage-in-portal.md).

Para información sobre cómo autenticar a usuarios de aplicaciones de API, consulte [Autenticación para aplicaciones de API y aplicaciones móviles en el Servicio de aplicaciones de Azure](../app-service/app-service-authentication-overview.md).

Para información sobre las características de las aplicaciones de API, consulte [¿Qué son las aplicaciones de API?](app-service-api-apps-why-best-platform.md).

<!---HONumber=AcomDC_0114_2016-->