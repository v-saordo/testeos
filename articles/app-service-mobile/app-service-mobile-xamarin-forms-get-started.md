<properties
	pageTitle="Introducción a Aplicaciones móviles con el uso de Xamarin.Forms"
	description="Siga este tutorial para empezar a usar Aplicaciones móviles de Azure para el desarrollo de Xamarin.Forms."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="11/23/2015"
	ms.author="normesta"/>

#Creación de una aplicación Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Información general

En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube a una aplicación móvil Xamarin.Forms con un back-end de Aplicaciones móviles de Azure. Creará tanto un back-end de aplicación móvil nuevo como una aplicación Xamarin.Forms simple de la _lista de tareas pendientes_ que almacene los datos de la aplicación en Azure.

Completar este tutorial es un requisito previo para todos los tutoriales de aplicaciones móviles para aplicaciones Xamarin.Forms.

##Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
 
* [Visual Studio Community 2013] o posterior. Si instala Visual Studio Community 2013, instale [Xamarin] por separado. Puede instalar las herramientas de Xamarin al instalar Visual Studio 2015.

* Un equipo Mac con [Xcode] v7.0 o versiones posteriores y [Xamarin Studio] instalados. Si piensa compilar la aplicación en un equipo Windows mediante Visual Studio, necesitará tener acceso a un equipo Mac en red para hacerlo.
 
>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](https://tryappservice.azure.com/?appServiceName=mobile), donde podrá crear inmediatamente una aplicación móvil de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Creación de un nuevo back-end de Aplicaciones móviles de Azure

Siga estos pasos para crear un nuevo back-end de aplicación móvil.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]


Ahora ha aprovisionado un back-end de aplicación móvil de Azure que puede usarse por las aplicaciones del cliente móvil. Después, descargará un proyecto de servidor para un back-end de "lista de tareas" sencillo y lo publicará en Azure.

## Configuración del proyecto de servidor

Para configurar el proyecto de servidor para que use el back-end de Node.js o. NET, siga los pasos que se indican a continuación.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

 
## (Opcional) Prueba del proyecto de back-end de forma local

Si eligió una configuración de back-end de .NET, tiene la opción de probar el back-end localmente.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]


##Descarga y ejecución de la solución Xamarin.Forms

Estas son algunas opciones. Puede descargar la solución en un equipo Mac y abrirla en Xamarin Studio, o descargarla en un equipo Windows y abrirla en Visual Studio. También puede usar ambos entornos y alternar entre ellos. Tenga en cuenta estas cosas:

* Es más fácil ejecutar el proyecto de iOS de la solución en un equipo Mac. Puede usar Visual Studio en el equipo Windows si lo desea, pero es un poco más complicado porque tiene que conectarse a un equipo Mac en red. Si está interesado en hacerlo, consulte [Installing Xamarin.iOS on Windows] (Instalación de Xamarin.iOS en Windows).
* Puede ejecutar el proyecto de Android en un equipo Mac o un equipo Windows.
* Los proyectos de Windows se pueden ejecutar solo mediante Visual Studio en un equipo Windows.

Con todo esto aclarado, continuemos.

 1. En el equipo Mac o el equipo Windows, abra el [Portal de Azure] en una ventana del explorador.
 2. En la hoja Configuración de la aplicación móvil, haga clic en **Introducción** (en Móvil) > **Xamarin.Forms**. En el paso 3, haga clic en **Crear una nueva aplicación**, en caso de que no esté seleccionado. A continuación, haga clic en el botón **Descargar**.

    De este modo, se descarga un proyecto que contiene la aplicación cliente que está conectada a la aplicación móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

 3. Extraiga el proyecto descargado y, después, ábralo en Xamarin Studio o Visual Studio.

	![][9]

	![][8]

##(Opcional) Ejecución del proyecto de iOS

Esta sección trata de la ejecución del proyecto de iOS de Xamarin para dispositivos iOS. Puede omitir esta sección si no está trabajando con dispositivos iOS.

####En Xamarin Studio

1. Haga clic con el botón derecho en el proyecto de iOS y, después, haga clic en **Establecer como proyecto de inicio**.
2. En el menú **Ejecutar**, haga clic en **Iniciar depuración** para compilar el proyecto e iniciar la aplicación en el emulador de iPhone.

####En Visual Studio
1. Haga clic con el botón derecho en el proyecto de iOS y, después, haga clic en **Establecer como proyecto de inicio**.
2. En el menú **Compilar**, haga clic en **Administrador de configuración**. 
3. En el cuadro de diálogo **Administrador de configuración**, active las casillas **Compilar** e **Implementar** del proyecto de iOS.
4. Presione la tecla **F5** para compilar el proyecto e iniciar la aplicación en el emulador de iPhone.

En la aplicación, escriba texto significativo, como _Aprender Xamarin_ y haga clic en el botón **+**.

![][10]

Esta acción envía una solicitud POST al nuevo back-end de aplicación móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El back-end de aplicación móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

> [AZURE.NOTE]
Encontrará el código con el que se accede al back-end de la aplicación móvil en el archivo de C# TodoItemManager.cs del proyecto de biblioteca de clases portable de la solución.

##(Opcional) Ejecución del proyecto de Android

Esta sección trata de la ejecución del proyecto de Android de Xamarin para Android. Puede omitir esta sección si no está trabajando con dispositivos Android.

####En Xamarin Studio

1. Haga clic con el botón derecho en el proyecto de Android y, después, haga clic en **Establecer como proyecto de inicio**.
2. En el menú **Ejecutar**, haga clic en **Iniciar depuración** para compilar el proyecto e iniciar la aplicación en un emulador de Android.

####En Visual Studio
1. Haga clic con el botón derecho en el proyecto de Android y, después, haga clic en **Establecer como proyecto de inicio**.
4. En el menú **Compilar**, haga clic en **Administrador de configuración**. 
5. En el cuadro de diálogo **Administrador de configuración**, active las casillas **Compilar** e **Implementar** del proyecto de Android.
6. Presione la tecla **F5** para compilar el proyecto e iniciar la aplicación en un emulador de Android.

En la aplicación, escriba texto significativo, como _Aprender Xamarin_ y haga clic en el botón **+**.

![][11]

Esta acción envía una solicitud POST al nuevo back-end de aplicación móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El back-end de aplicación móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

> [AZURE.NOTE]
Encontrará el código con el que se accede al back-end de la aplicación móvil en el archivo de C# TodoItemManager.cs del proyecto de biblioteca de clases portable de la solución.


##(Opcional) Ejecución del proyecto de Windows


Esta sección trata de la ejecución del proyecto de WinApp de Xamarin para dispositivos Windows. Puede omitir esta sección si no está trabajando con dispositivos Windows.


####En Visual Studio
1. Haga clic con el botón derecho en cualquiera de los proyectos de Windows y, después, haga clic en **Establecer como proyecto de inicio**.
4. En el menú **Compilar**, haga clic en **Administrador de configuración**. 
5. En el cuadro de diálogo **Administrador de configuración**, active las casillas **Compilar** e **Implementar** del proyecto de Windows que haya elegido.
6. Presione la tecla **F5** para compilar el proyecto e iniciar la aplicación en un emulador de Windows.

En la aplicación, escriba texto significativo, como _Aprender Xamarin_ y haga clic en el botón **+**.
	
Esta acción envía una solicitud POST al nuevo back-end de aplicación móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El back-end de aplicación móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

![][12]
	
> [AZURE.NOTE]
Encontrará el código con el que se accede al back-end de la aplicación móvil en el archivo de C# TodoItemManager.cs del proyecto de biblioteca de clases portable de la solución.

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal de Azure]: https://portal.azure.com/


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
 

<!---HONumber=AcomDC_0128_2016--->