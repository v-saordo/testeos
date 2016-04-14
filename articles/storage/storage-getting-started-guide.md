<properties 
	pageTitle="Introducción en cinco minutos a Almacenamiento de Microsoft Azure" 
	description="Mejore sus habilidades rápidamente en blobs, tablas y colas de Azure de Microsoft con los tutoriales de Almacenamiento de Azure, Visual Studio y con Emulador de almacenamiento de Azure. Ejecute su primera aplicación de Almacenamiento de Azure en cinco minutos." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="carmonm" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="get-started-article" 
	ms.date="02/14/2016" 
	ms.author="tamram"/>

# Introducción en cinco minutos a Almacenamiento de Azure 

## Información general

Es fácil empezar a realizar tareas de desarrollo con Almacenamiento de Azure. Este tutorial muestra cómo ejecutar rápidamente una aplicación de Almacenamiento de Azure. Usará las plantillas de inicio rápido incluidas con el SDK de Azure para. NET. Estos tutoriales contienen código listo para ejecutarse que muestra algunos escenarios básicos de programación con Almacenamiento de Azure.

Para obtener más información sobre Almacenamiento de Azure antes de entrar en el código, consulte [Pasos siguientes](#next-steps).

## Requisitos previos

Tendrá que cumplir los siguientes requisitos previos antes de empezar:

1. Para compilar y crear la aplicación, es necesario tener [Visual Studio](https://www.visualstudio.com/) instalado en el equipo. 

2. Instale la versión más reciente de [SDK de Azure para .NET](https://azure.microsoft.com/downloads/). SDK incluye los proyectos de ejemplo de la guía rápida de Azure, Emulador de almacenamiento de Azure y el [Biblioteca de cliente de Almacenamiento de Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

3. Asegúrese de que tiene [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) instalado en su equipo ya que es necesario para los proyectos de ejemplo de la guía rápida de Azure que usaremos en este tutorial.

	Si no está seguro de qué versión de .NET Framework está instalada en su equipo, consulte [Cómo saber qué versiones de .NET Framework tengo instaladas](https://msdn.microsoft.com/vstudio/hh925568.aspx). O pulse el botón **Inicio** o la tecla de Windows y escriba **Panel de Control**. A continuación, haga clic en **Programas** > **Programas y características**, y compruebe si .NET Framework 4.5 aparece en la lista de los programas instalados.

4. Necesitará una suscripción de Azure y una cuenta de Almacenamiento de Azure.

    - Para obtener una suscripción de Azure, consulte [Prueba gratuita](https://azure.microsoft.com/pricing/free-trial/), [Opciones de compra](https://azure.microsoft.com/pricing/purchase-options/) y [Ofertas para miembros](https://azure.microsoft.com/pricing/member-offers/) (para miembros de MSDN, Microsoft Partner Network, BizSpark y otros programas de Microsoft).
    - Para crear una cuenta de almacenamiento en Azure, consulte [Creación de una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account).

## Ejecución de la primera aplicación de Almacenamiento de Azure en Almacenamiento de Azure en la nube

Una vez que tenga la cuenta, cree una sencilla aplicación de Almacenamiento de Azure con uno de los proyectos de ejemplo del tutorial de Azure en Visual Studio. Este tutorial se centra en los proyectos de ejemplo para Almacenamiento de Azure: **Almacenamiento de Azure: blobs**, **Almacenamiento de Azure: archivos**, **Almacenamiento de Azure: colas** y **Almacenamiento de Azure: tablas**:

1. Inicie Visual Studio.
2. En el menú **Archivo**, haga clic en **Nuevo proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Instalados** > **Plantillas** > **Visual C#**> **Nube** > **Tutoriales** > **Servicios de datos**. Seleccione una de las siguientes plantillas: **Almacenamiento de Azure: blobs**, **Almacenamiento de Azure: archivos**, **Almacenamiento de Azure: colas** o **Almacenamiento de Azure: tablas**. b. Asegúrese de que **.NET Framework 4.5** sea el marco de trabajo de destino seleccionado.
	- 3\.c. Especifique un nombre para el proyecto y cree la nueva solución de Visual Studio, como se muestra aquí:
	
	![Tutoriales de Azure][Image1]

Es aconsejable revisar el código fuente antes de ejecutar la aplicación. Para revisar el código, seleccione **Explorador de soluciones** en el menú **Vista** en Visual Studio. A continuación, haga doble clic en el archivo Program.cs.

A continuación, ejecute la aplicación de ejemplo:

1.	En Visual Studio, seleccione **Explorador de soluciones** en el menú **Vista**. Abra el archivo App.config y marque como comentario la cadena de conexión del Emulador de almacenamiento de Azure:

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	Quite el comentario de la cadena de conexión del Servicio de almacenamiento de Azure y proporcione la clave de acceso y el nombre de la cuenta de almacenamiento en el archivo App.config:`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

	Para recuperar la clave de acceso de la cuenta de almacenamiento, consulte [Administración de claves de acceso de almacenamiento](storage-create-storage-account.md#manage-your-storage-access-keys).

3.	Una vez haya proporcionado el nombre de la cuenta de almacenamiento y la clave de acceso en el archivo App.config, en el menú **Archivo** haga clic en **Guardar todo** para guardar todos los archivos del proyecto.
4.	En el menú **Crear**, haga clic en **Crear solución**.
5.	En el menú **Depurar**, pulse **F11** para ejecutar la solución paso a paso o pulse **F5** para ejecutar la solución.


## Ejecución de la primera aplicación de Almacenamiento de Azure de forma local en el Emulador de almacenamiento de Azure

El [Emulador de almacenamiento de Azure](storage-use-emulator.md) proporciona un entorno local que emula los servicios de Blob, Cola y Tabla de Azure con fines de desarrollo. Puede usar el emulador de almacenamiento para probar la aplicación de almacenamiento de forma local, sin necesidad de crear una suscripción de Azure o una cuenta de almacenamiento y sin incurrir en ningún gasto.

Para probarlo, crearemos una sencilla aplicación de Almacenamiento de Azure con uno de los proyectos de ejemplo del tutorial de Inicio rápido de Azure en Visual Studio. Este tutorial se centra en los proyectos de ejemplo de **Almacenamiento de blobs de Azure**, **Almacenamiento de tablas de Azure** y **Almacenamiento de colas de Azure**:

1. Inicie Visual Studio.
2. En el menú **Archivo**, haga clic en **Nuevo proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Instalados** > **Plantillas** > **Visual C#**> **Nube** > **Tutoriales** > **Servicios de datos**. Seleccione una de las siguientes plantillas: **Almacenamiento de Azure: blobs**, **Almacenamiento de Azure: archivos**, **Almacenamiento de Azure: colas** o **Almacenamiento de Azure: tablas**. b. Asegúrese de que **.NET Framework 4.5** sea el marco de trabajo de destino seleccionado. c. Especifique un nombre para el proyecto y cree la nueva solución de Visual Studio, como se muestra aquí:
	
	![Tutoriales de Azure][Image1]

4.	En Visual Studio, seleccione **Explorador de soluciones** en el menú **Vista**. Abra el archivo App.config y marque como comentario la cadena de conexión de la cuenta de Almacenamiento de Azure si ha agregado uno. A continuación, quite el comentario de la cadena de conexión para el emulador de Almacenamiento de Azure:

	`<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

Es aconsejable revisar el código fuente antes de ejecutar la aplicación. Para revisar el código, seleccione **Explorador de soluciones** en el menú **Vista** en Visual Studio. A continuación, haga doble clic en el archivo Program.cs.

A continuación, ejecute la aplicación de ejemplo en Emulador de almacenamiento de Azure:

1.	Pulse el botón **Inicio** o la tecla de Windows, busque *Emulador de Almacenamiento de Microsoft Azure* e inicie la aplicación. Cuando se inicie el emulador, verá un icono y una notificación en el área de vista de tareas de Windows.
2.	En Visual Studio, haga clic en **Crear solución** en el menú **Crear**. 
3.	En el menú **Depurar**, pulse **F11** para ejecutar la solución paso a paso o pulse **F5** para ejecutar la solución de principio a fin.

## Pasos siguientes

Para obtener más información sobre Almacenamiento de Azure, consulte los siguientes recursos:

* [Introducción a Almacenamiento de Microsoft Azure](storage-introduction.md)
* [Introducción al Almacenamiento de blobs de Azure mediante .NET](storage-dotnet-how-to-use-blobs.md)
* [Introducción al Almacenamiento de tablas de Azure mediante .NET](storage-dotnet-how-to-use-tables.md)
* [Introducción al Almacenamiento en cola de Azure mediante .NET](storage-dotnet-how-to-use-queues.md)
* [Introducción a Almacenamiento de archivos de Azure en Windows](storage-dotnet-how-to-use-files.md)
* [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md)
* [Documentación de Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Biblioteca del cliente de Almacenamiento de Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [API de REST de servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
 

<!---HONumber=AcomDC_0309_2016-->