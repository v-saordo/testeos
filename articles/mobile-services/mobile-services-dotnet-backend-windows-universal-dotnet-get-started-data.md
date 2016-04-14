<properties
	pageTitle="Incorporación de Servicios móviles a una aplicación universal de la Tienda Windows existente | Microsoft Azure"
	description="Obtenga información acerca de cómo empezar a usar Servicios móviles para aprovechar datos en su aplicación de la Tienda Windows."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/10/2015"
	ms.author="glenga"/>

# Incorporación de Servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##Información general

Este tema muestra cómo usar Servicios móviles de Azure como origen de datos back-end para una aplicación de la Tienda Windows. En este tutorial descargará un proyecto de Visual Studio 2013 para una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil a la aplicación y verá los cambios que se hicieron en los datos durante la ejecución de la aplicación.

El servicio móvil que creará en este tutorial es un servicio móvil backend de .NET. El back-end de .NET permite usar lenguajes .NET y Visual Studio para la lógica de negocios del lado servidor en el servicio móvil. Este servicio puede ejecutarse y depurarse en el equipo local. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en JavaScript, consulte la versión back-end de JavaScript de este tema.

>[AZURE.NOTE]Este tema muestra cómo usar las herramientas de Visual Studio Professional 2013 Update 3 para conectar un nuevo servicio móvil a una aplicación universal para Windows. Se pueden seguir estos mismos pasos para conectar un servicio móvil a una aplicación de la Tienda Windows o de la Tienda de Windows Phone 8.1. Para conectar un servicio móvil a una aplicación de Windows Phone Silverlight 8.1 o Windows Phone 8.0, consulte [Introducción a los datos para Windows Phone](mobile-services-dotnet-backend-windows-phone-get-started-data.md).

> Si no puede actualizar a Visual Studio Professional 2013 Update 3 o prefiere agregar de forma manual el proyecto de servicio móvil a la solución de aplicación de la Tienda Windows, consulte [esta versión](../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md) del tema.

##Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio 2013</a> (Update 3 o una versión posterior).

##Descarga del proyecto GetStartedWithData

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##Creación de un servicio móvil nuevo desde Visual Studio

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

&nbsp;&nbsp;7. En el Explorador de soluciones, abra el archivo de código App.xaml.cs en la carpeta de proyecto GetStartedWithData.Shared y observe el nuevo campo estático que se agregó a la clase **App** en un bloque de compilación condicional de aplicación de la Tienda Windows, similar al ejemplo siguiente:

	public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient
	    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
	        "https://todolist.azure-mobile.net/",
	        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");


&nbsp;&nbsp;Este código proporciona acceso al nuevo servicio móvil en su aplicación mediante el uso de una instancia de la clase [MobileServiceClient](http://go.microsoft.com/fwlink/p/?LinkId=302030). El cliente se crea al suministrar el URI y la clave de aplicación del nuevo servicio móvil. Este campo estático está disponible para todas las páginas en la aplicación.

&nbsp;&nbsp;8. Haga clic con el botón derecho en el proyecto de aplicación de Windows Phone, haga clic en **Agregar** y **Servicio conectado...**, seleccione el servicio móvil que acaba de crear y haga clic en **Aceptar**. Se agrega el mismo código al archivo compartido App.xaml.cs, aunque esta vez en un bloque de compilación condicional de aplicación de Windows Phone.

En este punto, se conectan al nuevo servicio móvil las aplicaciones de la Tienda Windows y de la Tienda de Windows Phone. El paso siguiente consiste en probar el nuevo proyecto de servicio móvil.


##Prueba del proyecto de servicio móvil de forma local

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]


##Actualización de la aplicación para utilizar el servicio móvil

En esta sección, se actualiza la aplicación universal para Windows para usar el servicio móvil como servicio back-end para la aplicación. Solo debe realizar cambios en el archivo de proyecto MainPage.cs de la carpeta de proyecto GetStartedWithData.Shared.

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../../includes/mobile-services-windows-dotnet-update-data-app.md)]


##Publicación del servicio móvil en Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


##Prueba del servicio móvil hospedado en Azure

Ya puede probar las dos versiones de la aplicación universal para Windows con el servicio móvil hospedado en Azure.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]

##Vista de los datos almacenados en Base de datos SQL

[AZURE.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../../includes/mobile-services-dotnet-backend-view-sql-data.md)]

De este modo se finaliza el tutorial.

##Pasos siguientes

Este tutorial muestra los aspectos básicos de la habilitación de un proyecto de aplicación universal para Windows para trabajar con datos en Servicios móviles. A continuación, considere la posibilidad de obtener información acerca de uno de estos otros temas:

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

* [Referencia conceptual de Servicios móviles con C#](mobile-services-windows-dotnet-how-to-use-client-library.md) 
  <br/>Obtenga más información sobre cómo utilizar Servicios móviles con .NET.


<!-- Images. -->



<!-- URLs. -->
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Introducción a la autenticación]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Introducción a las notificaciones de inserción]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md

[Get started with offline data sync]: mobile-services-windows-store-dotnet-get-started-offline-data.md

[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkID=510826
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030

<!---HONumber=AcomDC_0128_2016-->