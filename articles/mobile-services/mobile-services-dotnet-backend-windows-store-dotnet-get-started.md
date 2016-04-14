<properties
	pageTitle="Introducción a los Servicios móviles para aplicaciones universales de Windows | Microsoft Azure"
	description="Siga este tutorial para aprender a usar Servicios móviles de Azure para el desarrollo de Windows Universal en C#."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/05/2016"
	ms.author="glenga"/>


# <a name="getting-started"> </a>Introducción a Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para ver la versión equivalente de este tema en Aplicaciones móviles, consulte [Creación de una aplicación para Windows](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md).

En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube para una aplicación universal de Windows con Servicios móviles de Azure. Las soluciones de aplicaciones universales de Windows incluyen proyectos para aplicaciones tanto de la Tienda Windows 8.1 como de la Tienda de Windows Phone 8.1 y un proyecto común compartido. Para obtener más información, consulte [Generación de aplicaciones universales dirigidas a Windows y a Windows Phone](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx).

Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *Lista de pendientes* que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil que cree utilizará los lenguajes .NET compatibles y recurrirá a Visual Studio para la lógica de negocios de servidor y para las tareas de administración. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en JavaScript, consulte la versión back-end de JavaScript de este tema.

>[AZURE.NOTE]En este tema se muestra cómo crear un proyecto de servicio móvil nuevo y la aplicación universal de Windows mediante el Portal de Azure clásico. Si usa Visual Studio 2013 con actualización 3, también puede agregar un proyecto de servicio móvil nuevo a una solución existente de Visual Studio. Para obtener más información, consulte [Incorporación de Servicios móviles a una aplicación existente](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md).

>Para agregar un servicio móvil a un proyecto de aplicación de Windows Phone 8.0 o Windows Phone Silverlight 8.1, consulte vea [Incorporación de Servicios móviles a una aplicación de Windows Phone existente](mobile-services-windows-phone-get-started-data.md).

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started%2F).
* [Visual Studio 2013].

## Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Creación de una nueva aplicación universal de Windows

Después de crear el servicio móvil, puede seguir una sencilla introducción rápida en el Portal de Azure clásico para crear una aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección, creará una nueva aplicación universal de Windows que se conecta al servicio móvil.

1. En el [Portal de Azure clásico], haga clic en **Servicios móviles** y luego en el servicio móvil que acaba de crear.

2. En la pestaña de inicio rápido, haga clic en **Windows** en **Seleccionar plataforma** y, a continuación, expanda **Crear una nueva aplicación de la Tienda Windows**.

   	Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación de la Tienda Windows conectada al servicio móvil.

  	![Pasos de inicio rápido para Servicios móviles](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)

3. Si todavía no lo ha hecho, descargue e instale [Visual Studio 2013] en el equipo local o máquina virtual.

4. En **Descargar y ejecutar la aplicación y el servicio localmente**, seleccione un idioma para la aplicación de la Tienda Windows y, a continuación, haga clic en **Descargar**.

  	De esta forma, se descarga una solución que contiene proyectos tanto para el servicio móvil como para la aplicación _To do list_ de ejemplo conectada a su servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

## Prueba de la aplicación con el servicio móvil local

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[AZURE.NOTE]Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo MainPage.xaml.cs.


## Publicación del servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


<ol start="4">
<li><p>En el proyecto de código compartido, abra el archivo App.xaml.cs, busque el código que crea una instancia de <a href="http://msdn.microsoft.com/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a>, convierta en comentario el código que crea este cliente mediante <em>localhost</em> y quite la marca de comentario del código que crea el cliente con la dirección URL del servicio móvil remoto, que debe tener el siguiente aspecto:</p>

        <pre><code>public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXX-APPLICATION-KEY-XXXXX");</code></pre>

	<p>El cliente ahora tendrá acceso al servicio móvil publicado en Azure.</p></li>
</ol>

## Prueba de la aplicación con el servicio móvil hospedado en Azure

Ahora que el servicio móvil se ha publicado y el cliente está conectado al servicio móvil remoto hospedado en Azure, podemos ejecutar la aplicación mediante Azure para el almacenamiento de elementos.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]


## Pasos siguientes
Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

* [Incorporación de Servicios móviles a una aplicación existente][Get started with data] <br/>Obtenga más información sobre cómo almacenar y consultar datos con Servicios móviles.

* [Introducción a la sincronización de datos sin conexión] <br/>Obtenga información sobre cómo usar la sincronización de datos sin conexión para mejorar la capacidad de respuesta y reforzar la solidez de su aplicación.

* [Incorporación de autenticación a la aplicación de Servicios móviles][Get started with authentication] <br/>Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Incorporación de notificaciones de inserción a la aplicación][Get started with push notifications] <br/>Aprenda a enviar una notificación push muy básica a la aplicación.

* [Solución de problemas de un back-end de .NET de Servicios móviles] <br/> Aprenda a diagnosticar y corregir los problemas que pueden surgir con un back-end de .NET de Servicios móviles.

Para obtener más información acerca de las aplicaciones universales de Windows, consulte [Compatibilidad de plataformas de varios dispositivos desde un único servicio móvil](mobile-services-how-to-use-multiple-clients-single-service.md#shared-vs).

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->

<!-- Images. -->



<!-- URLs. -->
[Visual Studio 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Get started with data]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md
[Get started with data]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Introducción a la sincronización de datos sin conexión]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[Get started with authentication]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Portal de Azure clásico]: https://manage.windowsazure.com/
[Solución de problemas de un back-end de .NET de Servicios móviles]: mobile-services-dotnet-backend-how-to-troubleshoot.md

<!---HONumber=AcomDC_0309_2016-->