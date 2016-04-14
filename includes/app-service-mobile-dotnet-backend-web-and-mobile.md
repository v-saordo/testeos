En este tema se muestra cómo crear una aplicación tanto con un cliente móvil como con un cliente web. Creará una aplicación móvil y una aplicación web, y utilizará la misma base de datos subyacente para ambas.

En primer lugar, creará tanto un back-end de aplicación móvil nuevo como una aplicación sencilla *To do list* que almacene los datos de la aplicación en el nuevo back-end de aplicación móvil. El back-end de aplicación móvil utiliza los idiomas que admite .NET para la lógica de negocios del lado del servidor. La aplicación cliente puede utilizar cualquier plataforma cliente compatible con la aplicación móvil, entre las que se incluyen iOS, Windows, Xamarin iOS y Xamarin Android.

A continuación, creará una aplicación web, para lo que usará la misma base de datos que para la aplicación móvil. Al final del tutorial, tendrá un cliente web y un cliente móvil que funcionan con los mismos datos.

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Creación de un nuevo back-end de aplicación móvil y un cliente

* Siga los pasos del tutorial [Creación de una aplicación móvil] para crear tanto un back-end de aplicación móvil y un cliente. Puede usar cualquier plataforma cliente compatible con las aplicaciones móviles, entre las que se incluyen iOS, Windows, Xamarin iOS, y Xamarin Android.

* Asegúrese de que ha implementado un back-end de aplicación móvil en Azure y de que conecta la aplicación cliente móvil al back-end hospedado. El proyecto de código de la aplicación móvil usa Entity Framework Code First e inicializa la base de datos después de la primera solicitud REST recibida desde una aplicación cliente móvil.

## Publicación de una API web TodoList desde Visual Studio

En esta sección, creará una nueva aplicación web mediante una solución de aplicación web de ejemplo. Modificará el ejemplo para usar el mismo nombre de esquema de base de datos y la misma cadena de conexión que la aplicación móvil.

>[AZURE.NOTE]Antes de completar estos pasos, asegúrese de que ha inicializado la base de datos del back-end de aplicación móvil, para lo que debe conectar un cliente a ella. De lo contrario, la aplicación web no podrá conectarse a la misma base de datos.

1. En el [Portal de Azure](https://portal.azure.com/), cree una nueva aplicación web usando el mismo grupo de recursos y plan de hospedaje que la aplicación móvil.

2. Descargue la solución de ejemplo [MultiChannelToDo] y ábrala en Visual Studio. La solución contiene un proyecto de API web y un proyecto de aplicación web para la interfaz de usuario de un cliente web.

3. En el proyecto de API web, edite MultiChannelToDoContext.cs. En `OnModelCreating`, actualice el nombre de esquema para que coincida con el nombre de la aplicación móvil:

        modelBuilder.HasDefaultSchema("your_mobile_app"); // your service name, replacing dashes with underscore

4. A continuación, obtendrá la cadena de conexión de la aplicación móvil del Portal de Azure:

    - Seleccione la aplicación móvil en el portal y haga clic la parte que tenga la etiqueta **Código del usuario**.

    - En la hoja que se abre, seleccione **Todas las configuraciones** y luego **Configuración de la aplicación**.

    - En **Cadenas de conexión**, haga clic en **Mostrar cadenas de conexión**. Copie el valor de **MS\_TableConnectionString**. Se trata de la cadena de conexión que usa la aplicación móvil para conectarse a la base de datos SQL.

5. En Visual Studio, haga clic con el botón derecho en el proyecto de API web y seleccione **Publicar**. Seleccione **Aplicaciones web de Azure** como destino de publicación y seleccione la aplicación web creada anteriormente. Haga clic en **Siguiente** hasta que llegue a la sección **Configuración** del Asistente para publicación web.

6. En la sección **Bases de datos**, pegue la cadena de conexión de la aplicación móvil como valor de **MultiChannelToDoContext**. Active únicamente la casilla **Usar esta cadena de conexión en tiempo de ejecución**.

7. Una vez que la API web se ha publicado correctamente en Azure, verá una página de confirmación. Copie la URL del servicio publicado.

## Publique la interfaz de usuario de un cliente web TodoList desde Visual Studio

En esta sección, utilizará una aplicación cliente web de ejemplo implementada con AngularJS. A continuación, utilizará Visual Studio para publicar el proyecto en una nueva aplicación web del Servicio de aplicaciones hospedada en Azure.

1. En Visual Studio, abra el proyecto **MultiChannelToDo.Web**. Edite el archivo `js/service/ToDoService.js` para agregar la URL a la API web que acaba de publicar:

        var apiPath = "https://your-web-api-site-name.azurewebsites.net";

2. Haga clic con el botón derecho en el proyecto **MultiChannelToDo.Web** y seleccione **Publicar**.

3. En el **Asistente para publicación web**, seleccione **Aplicación web de Azure** como destino de la publicación y cree una nueva aplicación web sin base de datos.

4. Una vez que el proyecto se haya publicado correctamente, verá la interfaz de usuario web en el explorador.

## Prueba de la aplicación móvil y de la aplicación web

1. En la interfaz de usuario web, agregue o edite algunas tareas pendientes.

    ![Vista de la aplicación web en el explorador](./media/app-service-mobile-dotnet-backend-web-and-mobile/web-app-in-browser.png)

2. Ejecute la aplicación móvil que creó en el tutorial [Creación de una aplicación móvil]. Verá las mismas tareas pendientes que en la aplicación web.

    ![Vista de la aplicación móvil de Xamarin](./media/app-service-mobile-dotnet-backend-web-and-mobile/xamarin-ios-quickstart-device.png)

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio de portal anterior al nuevo, consulte: [Referencia para navegar en el portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- Links -->

[MultiChannelToDo]: https://github.com/Azure/mobile-services-samples/tree/web-mobile/MultiChannelToDo
[Creación de una aplicación móvil]: ../article/app-service-mobile/app-service-mobile-xamarin-ios-get-started.md

<!---HONumber=AcomDC_1203_2015-->