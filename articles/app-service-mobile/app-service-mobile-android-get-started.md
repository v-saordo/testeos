<properties
    pageTitle="Creación de una aplicación de Android en aplicaciones móviles del Servicio de aplicaciones de Azure | Microsoft Azure"
    description="Siga este tutorial para aprender a usar back-ends de aplicación móvil de Azure para el desarrollo de Android"
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="02/04/2016"
    ms.author="yuaxu"/>

#Creación de una aplicación de Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Información general

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación móvil de Android con un back-end de la aplicación móvil de Azure. Creará tanto un back-end de aplicación móvil nuevo como una aplicación de Android simple de la _lista de tareas pendientes_ que almacene los datos de la aplicación en Azure.

Completar este tutorial es un requisito previo para todos los demás tutoriales de Android sobre cómo usar la característica Aplicaciones móviles en el Servicio de aplicaciones de Azure.

## Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* [Herramientas para desarrolladores de Android](https://developer.android.com/sdk/index.html), que incluyen el entorno de desarrollo integrado de Android Studio y la plataforma de Android más reciente.
* El SDK de Android móvil de Azure, al que se hace automáticamente referencia como parte del proyecto de inicio rápido que puede descargar.
* Un equipo con [Visual Studio Community 2013] o más reciente; no es necesario para un back-end de Node.js.
* Una [cuenta de Azure activa](https://azure.microsoft.com/pricing/free-trial/).

## Creación de un nuevo back-end de Aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Configuración del proyecto de servidor

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Descarga y ejecución de la aplicación de Android

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_0224_2016-->