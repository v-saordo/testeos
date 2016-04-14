<properties
	pageTitle="Uso de Almacenamiento de Azure en las aplicaciones de la Tienda Windows | Microsoft Azure"
	description="Obtenga información acerca de cómo crear una aplicación de la Tienda Windows que usa Almacenamiento de blobs, Almacenamiento de colas, Almacenamiento de tablas o Almacenamiento de archivos de Azure."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm" />
<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="tamram"/>
	
# Uso de Almacenamiento de Azure en las aplicaciones de la Tienda Windows

## Información general

Esta guía le muestra cómo comenzar con el desarrollo de una aplicación de la Tienda Windows que haga uso del almacenamiento de Azure.

## Descarga de las herramientas necesarias

- [Visual Studio 2012](http://msdn.microsoft.com/library/windows/apps/br211384) hace que sea fácil compilar, depurar, localizar, empaquetar e implementar aplicaciones de la Tienda Windows.
- La [Biblioteca de cliente de Almacenamiento de Azure para Windows en tiempo de ejecución](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx) proporciona una biblioteca de clases para trabajar con Almacenamiento de Azure.
- Las [Herramientas de servicios de datos de WCF para las aplicaciones de la Tienda Windows](http://www.microsoft.com/download/details.aspx?id=30714) amplían la experiencia de incorporación de referencia de servicios con compatibilidad con OData de cliente para las aplicaciones de la Tienda Windows en Visual Studio 2012 y versiones posteriores.

## Desarrollo de aplicaciones

### Preparación

Cree un nuevo proyecto de aplicaciones de la Tienda Windows en Visual Studio 2012 o posterior:

![store-apps-storage-vs-project][store-apps-storage-vs-project]

A continuación, agregue una referencia a la biblioteca de cliente de Almacenamiento de Azure; para ello, haga clic con el botón derecho en **Referencias**, a continuación, en **Agregar referencia** y vaya a la biblioteca de cliente de Almacenamiento para Windows en tiempo de ejecución que ha descargado:

![store-apps-storage-choose-library][store-apps-storage-choose-library]

### Uso de la biblioteca con los servicios BLOB y Cola

En este momento, la aplicación está preparada para llamar a los servicios Blob de Azure y Cola. Agregue las siguientes instrucciones **using** de manera que pueda hacerse referencia directamente a los tipos de Almacenamiento de Azure:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

A continuación, agregue un botón a la página. Agregue el siguiente código al evento **Click** y modifique el método del controlador de eventos con la palabra clave [async](http://msdn.microsoft.com/library/vstudio/hh156513.aspx):

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

Este código supone que hay dos variables de cadena, *accountName* y *accountKey*. Representan el nombre de la cuenta de almacenamiento y la clave de la cuenta que está asociada con esa cuenta.

Compile y ejecute la aplicación. Si hace clic en el botón, se comprobará si existe un contenedor con el nombre *container1* en la cuenta y se creará si no existiera.

### Uso de la biblioteca con el servicio Tabla

Los tipos usados para comunicarse con el servicio Tabla de Azure dependen de la biblioteca de servicios de datos WCF para la aplicación de la Tienda Windows. A continuación, agregue una referencia a las bibliotecas WCF necesarias mediante la consola del administrador de paquetes:

![store-apps-storage-package-manager][store-apps-storage-package-manager]

Use el siguiente comando para que el administrador de paquetes apunte a la ubicación de la máquina:

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Este comando agregará automáticamente todas las referencias necesarias a su proyecto. Si no desea usar la consola del administrador de paquetes, puede agregar la carpeta NuGet de servicios de datos WCF en su máquina local a la lista de orígenes de paquetes y, a continuación, agregar la referencia a través de la interfaz de usuario como se describe en [Administración de paquetes NuGet mediante el cuadro de diálogo](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

Cuando haya hecho referencia al paquete NuGet de servicios de datos WCF, cambie el código en el evento **Click** del botón:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

Este código comprueba si la tabla con el nombre *table1* existe en su cuenta y, a continuación, se crea si no existiera.

También puede agregar una referencia a Microsoft.WindowsAzure.Storage.Table.dll que está disponible en el mismo paquete que descargó. Esta biblioteca contiene una funcionalidad adicional como consultas genéricas y de serialización basadas en reflejo. Tenga en cuenta que esta biblioteca no es compatible con JavaScript.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png

<!---HONumber=AcomDC_0114_2016-->