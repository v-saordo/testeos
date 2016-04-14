
Vuelva a Visual Studio y seleccione el proyecto de aplicación cliente de código compartido (su nombre es `<your app name>.Shared`).

1. Expanda el archivo **App.xaml** y, a continuación, abra el archivo **App.xaml.cs**. Busque la declaración del miembro de `MobileService` que se inicializa con una dirección URL de localhost. Convierta esta declaración en comentario (con `CTRL+K,CTRL+C`) y quite la marca de comentario de la declaración (`CTRL+K,CTRL+U`) que establece la conexión con el servicio hospedado:

        // This MobileServiceClient has been configured to communicate with your local
        // test project for debugging purposes.
        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "http://localhost:58454"
        //);

        // This MobileServiceClient has been configured to communicate with the Azure Mobile Service and
        // Azure Gateway using the application key. You're all set to start working with your Mobile Service!
        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://mymobileapp-code.azurewebsites.net",
            "https://myresourcegroupgateway.azurewebsites.net/Microsoft.Azure.AppService.ApiApps.Gateway",
            "XXXX-APPLICATION-KEY-XXXXX"
        );

2. Presione la tecla F5 para recompilar el proyecto e inicie la aplicación de la Tienda Windows, que debería ser el proyecto inicial predeterminado.

2. En la aplicación, escriba texto significativo, como *Realice el tutorial*, en el cuadro de texto **Insertar un TodoItem** y, a continuación, haga clic en **Guardar**.

	![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-startup.png)

	Esta acción envía una solicitud POST al nuevo back-end de aplicación móvil hospedado en Azure.

3. Detenga la depuración y cambie el proyecto de inicio predeterminado en la solución universal de Windows a la aplicación de la Tienda de Windows Phone (haga clic con el botón derecho en el proyecto `<your app name>.WindowsPhone` y luego haga clic en **Establecer como proyecto de inicio**) y, a continuación vuelva a presionar F5.

	![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-completed-wp8.png)

	Tenga en cuenta que los datos guardados en el paso anterior se cargan desde la aplicación móvil después de que se inicie la aplicación de Windows.

<!---HONumber=Oct15_HO3-->