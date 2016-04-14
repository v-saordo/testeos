<properties
	pageTitle="Incorporación de Servicios móviles a una aplicación existente (WP8) | Microsoft Azure"
	description="Obtenga información acerca de cómo empezar a usar los datos de la aplicación de Windows Phone 8 de Servicios móviles de Azure."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="glenga"/>


# Incorporación de Servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##Información general

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de Windows Phone 8. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el [Portal de Azure clásico] para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

También puede ver a Nick Harris realizar una demostración de esto en el siguiente vídeo:
>[AZURE.VIDEO mobile-get-started-with-data-windows-phone]

##Requisitos previos

+ Visual Studio 2012 Express para Windows Phone 8 y el [SDK de Windows Phone 8] en Windows 8. Para completar este tutorial para crear una aplicación de Windows Phone 8.1, debe utilizar Visual Studio 2013 Update 2 o una versión posterior.

+ Una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Farticles%2Fdocumentation%2Fmobile-services-windows-phone-get-started-data%2F).

##<a name="download-app"></a>Descarga del proyecto GetStartedWithData

Este tutorial se basa en la [aplicación GetStartedWithData][Developer Code Samples site], un proyecto de aplicación Silverlight de Windows Phone 8.

1. Descargue el proyecto de aplicación de ejemplo GetStartedWithData en el [sitio de ejemplos de código para desarrolladores].

	>[AZURE.NOTE]Para crear una aplicación Silverlight de Windows Phone 8.1, en Windows Phone 8.1 cambie el SO de destino en el proyecto de aplicación Silverlight de Windows Phone 8 descargado. Para crear una aplicación de la Tienda de Windows Phone, descargue la [versión de la aplicación de Windows Phone](http://go.microsoft.com/fwlink/p/?LinkId=397372) del proyecto de aplicación de ejemplo GetStartedWithData.

2. En Visual Studio, abra el proyecto descargado y examine el archivo MainPage.xaml.cs.

   	Observe que los objetos **TodoItem** agregados se almacenan en **ObservableCollection&lt;TodoItem&gt** en memoria.

3. Presione la tecla **F5** para recopilar el proyecto e iniciar la aplicación.

4. En la aplicación, escriba algún texto en el cuadro de texto y, a continuación, haga clic en el botón **Save**.

   	![][0]

   	Observe que el texto guardado se muestra en la lista a continuación.

##<a name="create-service"></a>Crear un nuevo servicio móvil en el Portal de Azure clásico

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Agregar una tabla nueva al servicio móvil

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>Actualización de la aplicación para usar el servicio móvil para el acceso a datos

Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1. En el **Explorador de soluciones** de Visual Studio, haga clic con el botón derecho en el nombre del proyecto y, a continuación, seleccione **Administrar paquetes de NuGet**.

2. En el panel izquierdo, seleccione la categoría **En línea**, busque `WindowsAzure.MobileServices`, haga clic en **Instalar** en el paquete **Servicios móviles de Azure** y luego acepte el contrato de licencia.

  	![][7]

  	De este modo, se agrega la biblioteca de cliente de Servicios móviles al proyecto.

3. En el [Portal de Azure clásico], haga clic en **Servicios móviles** y en el servicio móvil que acaba de crear.

4. Haga clic en la pestaña **Panel** y anote la **dirección URL del sitio**; a continuación, haga clic en **Administrar claves** y anote la **clave de la aplicación**.

   	![][8]

  	Necesitará estos valores para obtener acceso al servicio móvil desde su código de aplicación.

5. En Visual Studio, abra el archivo App.xaml.cs y agregue o quite la marca de comentario en la siguiente instrucción `using`:

       	using Microsoft.WindowsAzure.MobileServices;

6. En este mismo archivo, quite la marca de comentario del código que define la variable **MobileService** y proporcione la dirección URL y la clave de aplicación del servicio móvil en el constructor **MobileServiceClient**, en ese orden.

		//public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "AppUrl",
        //    "AppKey"
        //);

  	De esta forma, se crea una nueva instancia de **MobileServiceClient** que se usa para obtener acceso al servicio móvil.

6. En el archivo MainPage.cs, agregue o quite la marca de comentario en las siguientes instrucciones `using`:

       	using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json;

7. En esta carpeta DataModel, reemplace la definición de clase **TodoItem** por el siguiente código:

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

7. Comente la línea que define la colección **items** existente y luego quite la marca de comentario de las siguientes líneas:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable =
			App.MobileService.GetTable<TodoItem>();

   	Este código crea una colección de enlaces (**elementos**) compatible con los servicios móviles y una clase proxy para la tabla de base de datos de SQL **TodoItem** (**todoTable**).

7. En el método **InsertTodoItem**, quite la línea de código que define la propiedad **TodoItem**.**Id**, agregue el modificador **async** al método y quite la marca de comentario de la siguiente línea de código:

        await todoTable.InsertAsync(todoItem);

  	Este código inserta un elemento nuevo en la tabla.

8. En el método **RefreshTodoItems**, agregue el modificador **async** al método y quite la marca de comentario en la siguiente línea de código:

        items = await todoTable.ToCollectionAsync();

   	Esto define el enlace a la colección de elementos en todoTable, que contiene todos los objetos TodoItem devueltos por el servicio móvil.

9. En el método **UpdateCheckedTodoItem**, agregue el modificador **async** al método y quite la marca de comentario en la siguiente línea de código:

         await todoTable.UpdateAsync(item);

   	Esto envía una actualización de elemento al servicio móvil.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

##<a name="test-app"></a>Prueba de la aplicación con su servicio móvil nuevo

1. En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2. Al igual que anteriormente, escriba texto en el cuadro de texto y, a continuación, haga clic en **Save**.

   	Esto envía un elemento nuevo como inserción al servicio móvil.

3. Inicie sesión en el [Portal de Azure clásico], haga clic en **Servicios móviles **y en su servicio móvil.

4. Haga clic en la pestaña **Datos** y, a continuación, en **Examinar**.

   	![][9]

   	Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

De este modo se finaliza el tutorial.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de Windows Phone 8 para trabajar con datos en Servicios móviles. A continuación, considere la posibilidad de obtener información acerca de uno de estos otros temas:

* [Agregar autenticación a la aplicación](mobile-services-windows-phone-get-started-users.md) <br/>Aprenda a autenticar a los usuarios de su aplicación.

* [Agregar notificaciones de inserción a su aplicación](mobile-services-javascript-backend-windows-phone-get-started-push.md) <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.

* [Referencia conceptual de Servicios móviles con C#](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>Obtenga más información sobre cómo utilizar Servicios móviles con .NET.

<!-- Anchors. -->
[Download the Windows Phone 8 app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png
[7]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
[8]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. -->

[Portal de Azure clásico]: https://manage.windowsazure.com/
[SDK de Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=271146
[sitio de ejemplos de código para desarrolladores]: http://go.microsoft.com/fwlink/p/?LinkId=271146

<!---HONumber=AcomDC_0128_2016-->