<properties
	pageTitle="Crear una aplicación universal de Windows en tiempo de ejecución 8.1 en las aplicaciones móviles del Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Siga este tutorial para aprender a usar back-ends de aplicación móvil de Azure para el desarrollo de la Tienda Windows en C#, Visual Basic o JavaScript."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/04/2016"
	ms.author="glenga"/>

#Creación de una aplicación para Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Información general

En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube para una aplicación universal de Windows. Para obtener más información, consulte [¿Qué son las aplicaciones móviles?](app-service-mobile-value-prop.md)

[AZURE.INCLUDE [app-service-mobile-windows-universal-get-started](../../includes/app-service-mobile-windows-universal-get-started.md)]

##Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] o versión posterior.

>[AZURE.NOTE] Si desea empezar a usar el Servicio de aplicaciones de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](https://tryappservice.azure.com/?appServiceName=mobile). Allí puede crear de forma inmediata una aplicación móvil de corta duración para iniciarse en el Servicio de aplicaciones, no se requiere tarjeta de crédito y no se establece ningún compromiso.

##Creación de un nuevo back-end de Aplicaciones móviles de Azure

Siga estos pasos para crear un nuevo back-end de aplicación móvil.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Ahora ha aprovisionado un back-end de aplicación móvil de Azure que puede usarse por las aplicaciones del cliente móvil. Después, descargará un proyecto de servidor para un back-end de "lista de tareas" sencillo y lo publicará en Azure.

## Configuración del proyecto de servidor

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

##Descarga y ejecución del proyecto de cliente

Una vez configurado el back-end de aplicación móvil, puede crear una nueva aplicación cliente o modificar una aplicación existente para conectarse a Azure. En esta sección, descargará un proyecto de plantilla de aplicación universal Windows que se personaliza para conectarse al back-end de aplicación móvil.

1. De nuevo en la hoja **Introducción** para el back-end de aplicación móvil, haga clic en **Crear una nueva aplicación** > **Descargar** y extraiga los archivos de proyecto comprimidos en el equipo local.

3. (Opcional) Agregue el proyecto de aplicación universal Windows a la solución con el proyecto de servidor. Esto hace que sea más fácil depurar y probar la aplicación y el back-end en la misma solución de Visual Studio, si decide hacer esto.

4. Con la aplicación de la Tienda Windows como proyecto de inicio, presione la tecla F5 para recompilar el proyecto e inicie la aplicación de la Tienda Windows.

5. En la aplicación, escriba texto significativo, como *Realice el tutorial*, en el cuadro de texto **Insertar un TodoItem** y, a continuación, haga clic en **Guardar**.

	![](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

	Esta acción envía una solicitud POST al nuevo back-end de aplicación móvil hospedado en Azure.

6. Detenga la depuración, haga clic con el botón derecho en el proyecto `<your app name>.WindowsPhone`, haga clic en **Establecer como proyecto de inicio** y luego presione F5 de nuevo.

	![](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-wp8.png)

	Tenga en cuenta que los datos guardados en el paso anterior se cargan desde la aplicación móvil después de que se inicie la aplicación de Windows.

##Pasos siguientes

* [Incorporación de autenticación a la aplicación ](app-service-mobile-windows-store-dotnet-get-started-users.md) <br/>Obtenga información acerca de cómo autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Incorporación de notificaciones de inserción a la aplicación](app-service-mobile-windows-store-dotnet-get-started-push.md) <br/>Aprenda a enviar una notificación push muy básica a la aplicación.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!----HONumber=AcomDC_0211_2016-->