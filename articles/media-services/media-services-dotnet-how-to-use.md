<properties 
	pageTitle="Configuración del equipo para el desarrollo de Servicios multimedia con .NET" 
	description="Conozca los requisitos previos de Servicios multimedia usando el SDK de Servicios multimedia para .NET. Aprenda también a crear una aplicación de Visual Studio." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>

#Desarrollo de Servicios multimedia con .NET

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

En este tema se explica cómo empezar a desarrollar aplicaciones de Servicios multimedia con .NET.

La **biblioteca del SDK de Servicios multimedia de Azure para .NET** le permite programar en los Servicios multimedia mediante. NET. Para que el desarrollo con .NET sea aún más fácil, se proporciona la biblioteca **Extensiones del SDK de Servicios multimedia de Azure para .NET**. Esta biblioteca contiene un conjunto de métodos de extensión y funciones auxiliares que simplifican el código de .NET. Las dos bibliotecas están disponibles a través de **NuGet** y **GitHub**.


##Requisitos previos

-   Una cuenta de Servicios multimedia en una suscripción de Azure nueva o existente. Consulte el tema [Cómo crear una cuenta de Servicios multimedia](media-services-create-account.md).
-   Sistemas operativos: Windows 10, Windows 7, Windows 2008 R2 o Windows 8.
-   .NET Framework 4.5.
-    Visual Studio 2015, Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express).


##Creación y configuración de un proyecto de Visual Studio

En esta sección se muestra cómo crear un proyecto en Visual Studio y configurarlo para el desarrollo de Servicios multimedia. En este caso, el proyecto es una aplicación de consola de C# Windows, pero los pasos de configuración que se muestran aquí se aplican a otros tipos de proyectos que puede crear para las aplicaciones de Servicios multimedia (por ejemplo, una aplicación de Windows Forms o una aplicación web de ASP.NET).

En esta sección se muestra cómo usar **NuGet** para agregar el SDK de Servicios multimedia para .NET y otras bibliotecas dependientes.

También puede obtener los bits más recientes del SDK de Servicios multimedia para .NET desde GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) y [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), compilar la solución y agregar las referencias al proyecto del cliente. Tenga en cuenta que todas las dependencias necesarias se descargan y extraen automáticamente.

1. Cree una nueva Aplicación de consola de C# en Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 SP1. Escriba el **nombre**, la **ubicación** y el **nombre de la solución** y, a continuación, haga clic en Aceptar.

2. Compile la solución.

2. Use **NuGet** para instalar y agregar **Extensiones del SDK de Servicios multimedia de Azure para .NET**. Al instalar este paquete, también se instala el **SDK de Servicios multimedia para .NET** y agrega todas las demás dependencias necesarias.
1. Asegúrese de que tiene instalada la versión más reciente de NuGet. Para más información e instrucciones de instalación, consulte [NuGet](http://nuget.codeplex.com/).

2. En el Explorador de soluciones, haga clic con el botón secundario en el nombre del proyecto y elija Administrar paquetes de NuGet.

Aparecerá el cuadro de diálogo Administrar paquetes de NuGet.

3. En la galería en línea, busque Extensiones de Servicios multimedia de Azure, elija Extensiones del SDK de Servicios multimedia de Azure para .NET y luego haga clic en el botón Instalar.

El proyecto se modifica y se agregan referencias a Extensiones del SDK de Servicios multimedia para .NET, al SDK de .NET de Servicios multimedia y a otros ensamblados dependientes.

4. Para promover un entorno de desarrollo más limpio, considere la posibilidad de habilitar la restauración de paquetes de NuGet. Para obtener más información, consulte [Restauración de paquetes de NuGet](http://docs.nuget.org/consume/package-restore).

3. Agregue una referencia al ensamblado **System.Configuration**. Este ensamblado contiene la clase de Configuración del sistema.**Administrador de configuración** que se usa para tener acceso a los archivos de configuración (por ejemplo, App.config).

Para agregar referencias usando el cuadro de diálogo Administrar referencias, haga lo siguiente:

1. En el Explorador de soluciones, haga clic con el botón secundario en el nombre del proyecto. A continuación, seleccione Agregar y Referencias.

Aparecerá el cuadro de diálogo Administrar referencias.

2. En los ensamblados de .NET Framework, busque y seleccione el ensamblado System.Configuration.
3. Presione Aceptar.


4. Abra el archivo App.config (agregue el archivo al proyecto si no se ha agregado de forma predeterminada) y agregue una sección *appSettings* al archivo. Establezca los valores de la clave de nombre y la cuenta de cuenta de Servicios multimedia de Azure, tal como se muestra en el ejemplo siguiente.

Para obtener la información del **nombre de cuenta** y la **clave de cuenta**, abra el **Portal de Azure clásico**, seleccione la cuenta de Servicios multimedia y haga clic en el botón **ADMINISTRAR CLAVES**.


<configuration> ... <appSettings> <add key="MediaServicesAccountName" value="Media-Services-Account-Name" /> <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" /> </appSettings>
	  
	</configuration>

5. Sobrescriba los valores existentes siguiendo las instrucciones al comienzo del archivo Program.cs con el siguiente código.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;

En este punto, está listo para iniciar el desarrollo de una aplicación de Servicios multimedia.



##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->