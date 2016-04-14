
A continuación, debe cambiar la manera en que se registran las notificaciones de inserción para asegurarse de que el usuario se autentique antes de que se intente el registro.

1. En el Explorador de soluciones de Visual Studio, abra el archivo de proyecto app.xaml.cs y en el controlador de eventos **Application\_Launching**, convierta en comentario o elimine la llamada al método **AcquirePushChannel**. 
 
2. Cambie la accesibilidad del método **AcquirePushChannel** de `private` a `public` y agregue el modificador `static`.

3. Abra el archivo de proyecto MainPage.xaml.cs y reemplace el método **OnNavigatedTo** por lo siguiente:

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            App.AcquirePushChannel();
            RefreshTodoItems();
        }

<!---HONumber=Oct15_HO3-->