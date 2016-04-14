<properties 
	pageTitle="Carga de imágenes en el almacenamiento de blobs de Azure desde una aplicación universal de Windows | Microsoft Azure" 
	description="Obtenga información acerca de cómo usar un servicio móvil de back-end de JavaScript para cargar imágenes al almacenamiento de blobs de Azure y acceder a ellas desde la aplicación universal de Windows." 
	services="mobile-services,storage" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="glenga"/>

# Carga de imágenes al almacenamiento de blobs de Azure mediante Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##Información general 

Este tema muestra cómo utilizar Servicios móviles de Azure para permitir que su aplicación cargue y almacene imágenes generadas por el usuario en el almacenamiento de Azure. Servicios móviles utiliza una Base de datos SQL para almacenar datos. Sin embargo, los datos de objetos binarios grandes (BLOB) se almacenan de manera eficiente en el servicio de almacenamiento de blobs de Azure.

No puede distribuir de manera segura con la aplicación cliente las credenciales que se requieren para cargar con seguridad datos al servicio de almacenamiento de blobs. En lugar de eso, debe almacenar estas credenciales en su servicio móvil y usarlas para generar una firma de acceso compartido (SAS) que se utiliza para cargar una imagen nueva. Servicios móviles devuelve de manera segura a SAS, una credencial de expiración breve (en este caso de cinco minutos), a la aplicación cliente. Luego la aplicación utiliza esta credencial temporal para cargar la imagen. En este ejemplo, las descargas del servicio BLOB son públicas.

En este tutorial agregará funcionalidad a la aplicación de inicio rápido de Servicios móviles para tomar fotografías y cargar las imágenes a Azure a través de una SAS generada por Servicios móviles.

##Requisitos previos

Este tutorial requiere lo siguiente:

+ Microsoft Visual Studio 2013 Update 3 o una versión posterior.
+ [Cuenta de almacenamiento de Azure](../storage-create-storage-account.md)
+ Una cámara u otro dispositivo de captura de imágenes conectado a su equipo.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, debe leer [Introducción a los Servicios móviles].

##Actualización del script de inserción registrado en el Portal de Azure clásico

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-upload-to-blob-storage](../../includes/mobile-services-windows-universal-dotnet-upload-to-blob-storage.md)]

##Pasos siguientes

Ahora que ha podido cargar de manera segura imágenes al integrar su servicio móvil con el servicio BLOB, revise algunos de los otros temas relacionados con la integración y el servicio back-end:

+ [Programar trabajos de back-end en Servicios móviles]

  	Aprenda a utilizar la funcionalidad del programador de trabajos de Servicios móviles para definir el código de script de servidor que se ejecuta según una programación que define usted.

+ [Referencia del script del servidor de servicios móviles]

    Temas de referencia para utilizar scripts de servidor con la finalidad de ejecutar tareas del lado servidor e integración con otros componentes de Azure y recursos externos.
 
+ [Referencia conceptual de Servicios móviles con .NET]

    Obtenga más información sobre el uso de Servicios móviles con .NET.
  
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[Send email from Mobile Services with SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Programar trabajos de back-end en Servicios móviles]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Referencia del script del servidor de servicios móviles]: mobile-services-how-to-use-server-scripts.md
[Introducción a los Servicios móviles]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Referencia conceptual de Servicios móviles con .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
 

<!---HONumber=AcomDC_1203_2015-->