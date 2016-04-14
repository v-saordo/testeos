<properties
	pageTitle="Introducción a Servicios móviles para aplicaciones JavaScript para Tienda Windows | Servicios móviles de Azure"
	description="Siga este tutorial para aprender a usar Servicios móviles de Azure para el desarrollo de Tienda Windows en JavaScript."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="javascript"
	ms.topic="get-started-article"
	ms.date="03/06/2016"
	ms.author="glenga"/>

# Introducción a Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para ver la versión equivalente de este tema en Aplicaciones móviles, consulte [Creación de una aplicación para Windows](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md).

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación JavaScript para Tienda Windows con Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *Lista de pendientes* que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil que se creará utiliza JavaScript para la lógica de negocios de servidor.

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Visual Studio 2013 Express para Windows]

## Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Creación de una aplicación de la Tienda Windows

Después de crear el servicio móvil, puede seguir una sencilla introducción rápida en el Portal de Azure clásico para crear una aplicación JavaScript para Tienda Windows 8.1 que se conecte al servicio móvil.

1.  En el [Portal de Azure clásico], haga clic en **Servicios móviles** y luego en el servicio móvil que acaba de crear.


2. En la pestaña de inicio rápido, haga clic en **Windows** en **Seleccionar plataforma** y, a continuación, expanda **Crear una nueva aplicación de la Tienda Windows**.

3. Si todavía no lo ha hecho, descargue e instale [Visual Studio 2013][Visual Studio 2013 Express for Windows] en el equipo local o máquina virtual.

4. Haga clic en **Crear tabla TodoItem** para crear una tabla para almacenar los datos de la aplicación.

5. En **Descargar y ejecutar la aplicación**, seleccione un idioma para la aplicación y, a continuación, haga clic en **Descargar**.

  	De este modo se descarga el proyecto para la aplicación de *lista de pendientes* de muestra que está conectada al servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

## Ejecución de la aplicación de Windows

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1. Vaya a la ubicación donde guardó los archivos comprimidos del proyecto, expándalos en su equipo y abra el archivo de solución en Visual Studio.

2. Presione la tecla **F5** para recopilar el proyecto e iniciar la aplicación.

3. En la aplicación, escriba texto significativo, como *Realice el tutorial*, en **Insertar TodoItem** y, a continuación, haga clic en **Guardar**.

   	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y se muestran los datos en la segunda columna de la aplicación.

4. (Opcional) Ejecute de nuevo la aplicación y tenga en cuenta que los datos guardados en el paso anterior se cargan del servicio móvil después de que se inicie la aplicación.
 
4. De nuevo en el [Portal de Azure clásico], haga clic en la pestaña **Datos** y luego en la tabla **TodoItems**.

   	Esto le permite examinar los datos que la aplicación inserta en la tabla.

>[AZURE.NOTE] Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo default.js.

## Pasos siguientes
Ahora que completó el tutorial, aprenderá a trabajar con el [Cliente de Servicios móviles para HTML/JavaScript](mobile-services-html-how-to-use-client-library.md).

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Visual Studio 2013 Express para Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal de Azure clásico]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0309_2016-->