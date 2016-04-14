<properties
	pageTitle="Introducción a Aplicaciones móviles del Servicio de aplicaciones de Azure para aplicaciones Xamarin.iOS | Microsoft Azure"
	description="Siga este tutorial para empezar a usar Aplicaciones móviles para el desarrollo de Xamarin.iOS."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/13/2016"
	ms.author="normesta"/>


#Creación de una aplicación Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Información general

En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube a una aplicación móvil Xamarin.iOS con un back-end de Aplicaciones móviles de Azure. Creará tanto un back-end de aplicación móvil nuevo como una aplicación Xamarin.iOS simple de la _lista de tareas pendientes_ que almacene los datos de la aplicación en Azure.

Completar este tutorial es un requisito previo para todos los demás tutoriales de Xamarin.iOS sobre cómo usar la característica Aplicaciones móviles en el Servicio de aplicaciones de Azure.

##Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) o posterior. Si instala Visual Studio Community 2013, instale [Xamarin] por separado. Puede instalar las herramientas de Xamarin al instalar Visual Studio 2015.

* Un equipo Mac con [Xcode] v7.0 o versiones posteriores y [Xamarin Studio] instalados.

* Si planea compilar la aplicación en un equipo con Windows y Visual Studio, necesitará acceso a un equipo Mac en red con el host de compilación Xamarin.iOS para compilarla e implementarla. Para más información, consulte [Instalación Xamarin.iOS en Windows](http://developer.xamarin.com/guides/ios/getting_started/installation/windows/)

>[AZURE.NOTE] Si desea empezar a usar el Servicio de aplicaciones de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](https://tryappservice.azure.com/?appServiceName=mobile). Allí puede crear de forma inmediata una aplicación móvil de corta duración para iniciarse en el Servicio de aplicaciones, no se requiere tarjeta de crédito y no se establece ningún compromiso.

## Creación de un nuevo back-end de Aplicaciones móviles de Azure

Siga estos pasos para crear un nuevo back-end de aplicación móvil.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Configuración del proyecto de servidor

Ahora ha aprovisionado un back-end de aplicación móvil de Azure que puede usarse por las aplicaciones del cliente móvil. Después, descargará un proyecto de servidor para un back-end de "lista de tareas" sencillo y lo publicará en Azure.

Para configurar el proyecto de servidor para que use el back-end de Node.js o. NET, siga los pasos que se indican a continuación.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]


## (Opcional) Prueba del proyecto de back-end de forma local

Si eligió una configuración de back-end de .NET, tiene la opción de probar el back-end localmente.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]



## Descarga y ejecución de la aplicación Xamarin.iOS

1. En el equipo Mac, abra el [Portal de Azure] en una ventana del explorador.

	>[AZURE.NOTE] Es más fácil ejecutar la aplicación Xamarin.iOS en un equipo Mac. Puede ejecutar la aplicación Xamarin.iOS mediante Visual Studio en un equipo basado en Windows si lo desea, pero es un poco más complicado porque tiene que conectarse a un equipo Mac en red. Si está interesado en hacerlo, consulte [Installing Xamarin.iOS on Windows] (Instalación de Xamarin.iOS en Windows).

2. En la hoja de configuración de la aplicación móvil, haga clic en **Introducción** > **Xamarin.iOS**. En el paso 3, haga clic en **Crear una nueva aplicación**, en caso de que no esté seleccionado. A continuación, haga clic en el botón **Descargar**.

  	De este modo, se descarga un proyecto que contiene la aplicación cliente que está conectada a la aplicación móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

3. Extraiga el proyecto descargado y, después, ábralo en Xamarin Studio (o Visual Studio).

	![][9]

	![][8]

4. Presione la tecla F5 para compilar el proyecto e iniciar la aplicación en el emulador de iPhone.

5. En la aplicación, escriba texto significativo, como _Aprender Xamarin_, y haga clic en el botón **+**.

	![][10]

	Esta acción envía una solicitud POST al nuevo back-end de aplicación móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El back-end de aplicación móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

>[AZURE.NOTE]Puede revisar el código que obtiene acceso al back-end de aplicación móvil para consultar e insertar datos en el archivo de C# QSTodoService.cs.

##Pasos siguientes

* [Adición de la autenticación a la aplicación Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-users.md) <br/>Aprenda a autenticar a los usuarios de su aplicación mediante un proveedor de identidades.

* [Incorporación de notificaciones de inserción a la aplicación](app-service-mobile-xamarin-ios-get-started-push.md) <br/>Aprenda a enviar una notificación push muy básica a la aplicación.

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Portal de Azure]: https://portal.azure.com/


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/

<!---HONumber=AcomDC_0224_2016-->