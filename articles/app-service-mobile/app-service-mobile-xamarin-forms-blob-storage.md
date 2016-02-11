<properties
    pageTitle="Conexión a Almacenamiento de Azure en una aplicación Xamarin.Forms"
    description="Agregar imágenes a la aplicación móvil de lista de tareas pendientes de Xamarin.Forms mediante la conexión al almacenamiento de blobs de Azure"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="01/21/2015"
    ms.author="donnam"/>

#Conexión a Almacenamiento de Azure en una aplicación Xamarin.Forms

## Información general

El cliente de Aplicaciones móviles de Azure y el SDK de servidor admiten la sincronización sin conexión de datos estructurados con operaciones CRUD contra el punto de conexión /tables. Normalmente, estos datos se almacenan en una base de datos o un almacén similar y, por lo general, estos almacenes de datos no pueden almacenar datos binarios de gran tamaño de manera eficaz. Además, algunas aplicaciones tienen datos relacionados que se almacenan en cualquier parte (por ejemplo, almacenamiento de blobs, recursos compartidos de archivos), y resulta útil poder crear asociaciones entre registros del punto de conexión /tables y otros datos.

En este tema se muestra cómo agregar compatibilidad con imágenes al inicio rápido de lista de tareas pendientes de Aplicaciones móviles. Primero debe completar el tutorial [Creación de una aplicación Xamarin.Forms].

En este tutorial, creará una cuenta de almacenamiento y agregará una cadena de conexión al back-end de su aplicación móvil. A continuación, agregará una nueva herencia del nuevo tipo de aplicación móvil `StorageController<T>` al proyecto de servidor.

>[AZURE.TIP] Este tutorial incluye un [ejemplo complementario](https://azure.microsoft.com/documentation/samples/app-service-mobile-dotnet-todo-list-files/), que se pueden implementar en su propia cuenta de Azure.

## Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] o posterior.

* Un equipo Mac con [Xcode](https://go.microsoft.com/fwLink/?LinkID=266532) v7.0 o versiones posteriores y [Xamarin Studio](http://xamarin.com/download) instalados.

* Realización del tutorial [Creación de una aplicación Xamarin.Forms]. En este artículo se usa la aplicación realizada en ese tutorial.

>[AZURE.NOTE] Si desea empezar a usar el Servicio de aplicaciones de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](https://tryappservice.azure.com/?appServiceName=mobile). Allí puede crear de forma inmediata una aplicación móvil de corta duración para iniciarse en el Servicio de aplicaciones, no se requiere tarjeta de crédito y no se establece ningún compromiso.

## Crear una cuenta de almacenamiento

1. Cree una cuenta de almacenamiento siguiendo el tutorial [Crear una cuenta de almacenamiento de Azure]. 

2. En el Portal de Azure, diríjase a su cuenta de almacenamiento recién creada y haga clic en el icono **Claves**. Copie la **cadena de conexión primaria**.

3. Vaya al back-end de la aplicación móvil. En **Todas las configuraciones** -> **Configuración de la aplicación** -> **Cadena de conexión**, cree una nueva clave llamada `MS_AzureStorageAccountConnectionString` y use el valor copiado de la cuenta de almacenamiento. Utilice **Personalizado** como el tipo de clave.

## Agregar un controlador de almacenamiento a su proyecto de servidor

1. En Visual Studio, abra el proyecto de servidor .NET. Agregue el paquete NuGet [Microsoft.Azure.Mobile.Server.Files]. Asegúrese de seleccionar **Incluir versión preliminar**.

2. En Visual Studio, abra el proyecto de servidor .NET. Haga clic con el botón derecho en la carpeta **Controladores** y seleccione **Agregar** -> **Controlador** -> **Controlador de Web API 2 – en blanco**. Asigne al controlador el nombre `TodoItemStorageController`.

3. Agregue las siguientes instrucciones using:

        using Microsoft.Azure.Mobile.Server.Files;
        using Microsoft.Azure.Mobile.Server.Files.Controllers;

4. Cambie la clase base a `StorageController`:
    
        public class TodoItemStorageController : StorageController<TodoItem>

5. Agregue los siguientes métodos a la clase:

        [HttpPost]
        [Route("tables/TodoItem/{id}/StorageToken")]
        public async Task<HttpResponseMessage> PostStorageTokenRequest(string id, StorageTokenRequest value)
        {
            StorageToken token = await GetStorageTokenAsync(id, value);

            return Request.CreateResponse(token);
        }

        // Get the files associated with this record
        [HttpGet]
        [Route("tables/TodoItem/{id}/MobileServiceFiles")]
        public async Task<HttpResponseMessage> GetFiles(string id)
        {
            IEnumerable<MobileServiceFile> files = await GetRecordFilesAsync(id);

            return Request.CreateResponse(files);
        }

        [HttpDelete]
        [Route("tables/TodoItem/{id}/MobileServiceFiles/{name}")]
        public Task Delete(string id, string name)
        {
            return base.DeleteFileAsync(id, name);
        }

6. Publique el proyecto de servidor en el back-end de su aplicación móvil.

###Rutas registradas por el controlador de almacenamiento

El nuevo `TodoItemStorageController` expone dos recursos secundarios en el registro que administra:

- StorageToken

    + HTTP POST: crea un token de almacenamiento.
    
        `/tables/TodoItem/{id}/MobileServiceFiles`
    
- MobileServiceFiles

    + HTTP GET: recupera una lista de archivos asociados con el registro.
    
        `/tables/TodoItem/{id}/MobileServiceFiles`

    + HTTP DELETE: elimina el archivo especificado en el identificador de recurso de archivo.
    
        `/tables/TodoItem/{id}/MobileServiceFiles/{fileid}`

###Comunicación de cliente y servidor

Tenga en cuenta que `TodoItemStorageController` *no* tiene una ruta para la carga o descarga de un blob. La razón es que un cliente móvil interactúa *directamente* con el almacenamiento de blobs para realizar estas operaciones, después de obtener primero un token de SAS (firma de acceso compartido) para acceder de forma segura a un blob o un contenedor en particular. Se trata de un diseño de arquitectura importante, ya que, de lo contrario, el acceso al almacenamiento estaría limitado por la escalabilidad y la disponibilidad del back-end móvil. De este modo, al conectarse directamente a Almacenamiento de Azure, el cliente móvil puede aprovechar sus características, como el particionamiento automático o la distribución geográfica.

Una firma de acceso compartido ofrece acceso delegado a recursos en la cuenta de almacenamiento. Esto significa que puede conceder permisos limitados de los clientes a objetos en su cuenta de almacenamiento durante un período específico y con un conjunto determinado de permisos sin tener que compartir las claves de acceso a las cuentas. Para más información, consulte la [Firmas de acceso compartido, Parte 1: Descripción del modelo SAS].

El diagrama siguiente muestra las interacciones entre el cliente y el servidor. Antes de cargar un archivo, el cliente solicita al servicio un token de SAS. El servicio utiliza la cadena de conexión de almacenamiento para generar una nueva clave de SAS, que luego se devuelve al cliente. La clave de SAS tiene un límite de tiempo y restringe los permisos a solo un archivo o contenedor determinados. El cliente móvil utiliza entonces la clave de SAS y el SDK de cliente de Almacenamiento de Azure para cargar el archivo en el almacenamiento de blobs.

![Solicitud de un token de SAS](./media/app-service-mobile-xamarin-forms-blob-storage/storage-token-diagram.png)

## Actualización de la aplicación cliente para agregar compatibilidad con imágenes

Abra el proyecto de inicio rápido de Xamarin.Forms en Visual Studio o Xamarin Studio.

>[AZURE.NOTE] En este tutorial solo se incluyen instrucciones para las plataformas Android, iOS y Tienda Windows, no para Windows Phone.

###Incorporación de paquetes NuGet

Haga clic con el botón derecho en la solución y seleccione **Administrar paquetes NuGet para la solución**. Agregue los siguientes paquetes NuGet a **todos** los proyectos de la solución. No olvide activar **Incluir versión preliminar**.

  - [Microsoft.Azure.Mobile.Client.Files]

  - [Microsoft.Azure.Mobile.Client.SQLiteStore]

  - [PCLStorage]

Para mayor comodidad, en este ejemplo se utiliza la biblioteca [PCLStorage], pero no es necesaria para el SDK de cliente de Aplicaciones móviles de Azure.

[PCLStorage]: https://www.nuget.org/packages/PCLStorage/

###Agregar la interfaz IPlatform

Cree una nueva interfaz `IPlatform` en el proyecto de biblioteca portable principal. Se sigue el patrón [Xamarin.Forms DependencyService] para cargar la clase correcta específica de la plataforma en tiempo de ejecución. Más tarde agregará implementaciones específicas de la plataforma en cada uno de los proyectos de cliente.

1. Agregue las siguientes instrucciones using:

        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Sync;

2. Reemplace esta implementación por lo siguiente:

        public interface IPlatform
        {
            Task <string> GetTodoFilesPathAsync();

            Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata);

            Task<string> TakePhotoAsync(object context);

            Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename);
        }

###Add FileHelper class

1. Cree una nueva clase `FileHelper` en el proyecto de biblioteca portable principal. Agregue las siguientes instrucciones using:

        using System.IO;
        using PCLStorage;
        using System.Threading.Tasks;
        using Xamarin.Forms;

2. Agregue la definición de clase:

        public class FileHelper
        {
            public static async Task<string> CopyTodoItemFileAsync(string itemId, string filePath)
            {
                IFolder localStorage = FileSystem.Current.LocalStorage;

                string fileName = Path.GetFileName(filePath);
                string targetPath = await GetLocalFilePathAsync(itemId, fileName);

                var sourceFile = await localStorage.GetFileAsync(filePath);
                var sourceStream = await sourceFile.OpenAsync(FileAccess.Read);

                var targetFile = await localStorage.CreateFileAsync(targetPath, CreationCollisionOption.ReplaceExisting);

                using (var targetStream = await targetFile.OpenAsync(FileAccess.ReadAndWrite)) {
                    await sourceStream.CopyToAsync(targetStream);
                }

                return targetPath;
            }

            public static async Task<string> GetLocalFilePathAsync(string itemId, string fileName)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();

                string recordFilesPath = Path.Combine(await platform.GetTodoFilesPathAsync(), itemId);

                    var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(recordFilesPath);
                    if (checkExists == ExistenceCheckResult.NotFound) {
                        await FileSystem.Current.LocalStorage.CreateFolderAsync(recordFilesPath, CreationCollisionOption.ReplaceExisting);
                    }

                return Path.Combine(recordFilesPath, fileName);
            }

            public static async Task DeleteLocalFileAsync(Microsoft.WindowsAzure.MobileServices.Files.MobileServiceFile fileName)
            {
                string localPath = await GetLocalFilePathAsync(fileName.ParentId, fileName.Name);
                var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(localPath);

                if (checkExists == ExistenceCheckResult.FileExists) {
                    var file = await FileSystem.Current.LocalStorage.GetFileAsync(localPath);
                    await file.DeleteAsync();
                }
            }
        }

### Agregar un controlador de sincronización de archivos

Cree una nueva clase `TodoItemFileSyncHandler` en el proyecto de biblioteca portable principal. Esta clase contiene las devoluciones de llamada del SDK de Azure para notificar el código cuando se agrega o quita un archivo.

El SDK de cliente de Azure Mobile no almacena realmente datos de archivo: el SDK de cliente invoca la implementación de `IFileSyncHandler` que, a su vez, determina si los archivos se almacenan en el dispositivo local y cómo lo hacen.

1. Agregue las siguientes instrucciones using:

        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Xamarin.Forms;

2. Sustituya la definición de clase por el siguiente código:

        public class TodoItemFileSyncHandler : IFileSyncHandler
        {
            private readonly TodoItemManager todoItemManager;

            public TodoItemFileSyncHandler(TodoItemManager itemManager)
            {
                this.todoItemManager = itemManager;
            }

            public Task<IMobileServiceFileDataSource> GetDataSource(MobileServiceFileMetadata metadata)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
                return platform.GetFileDataSource(metadata);
            }

            public async Task ProcessFileSynchronizationAction(MobileServiceFile file, FileSynchronizationAction action)
            {
                if (action == FileSynchronizationAction.Delete) {
                    await FileHelper.DeleteLocalFileAsync(file);
                }
                else { // Create or update. We're aggressively downloading all files.
                    await this.todoItemManager.DownloadFileAsync(file);
                }
            }
        }

###Actualización de TodoItemManager

1. En **TodoItemManager.cs**, quite el comentario de la línea `#define OFFLINE_SYNC_ENABLED`.

2. En **TodoItemManager.cs**, agregue las siguientes instrucciones using:

        using System.IO;
        using Xamarin.Forms;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Eventing;

3. En el constructor de `TodoItemManager`, agregue lo siguiente después de la llamada a `DefineTable()`:

        // Initialize file sync
        this.client.InitializeFileSyncContext(new TodoItemFileSyncHandler(this), store);

4. En el constructor, reemplace la llamada a `InitializeAsync` por lo siguiente. De esta forma se tiene la seguridad de que cuando se modifican registros en el almacén local, hay devoluciones de llamada. La característica de sincronización de archivos utiliza estas devoluciones de llamada para desencadenar el controlador de sincronización de archivos.

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

5. En `SyncAsync()`, agregue lo siguiente después de la llamada a `PushAsync()`:

        await this.todoTable.PushFileChangesAsync();

6. Agregue los siguientes métodos a `TodoItemManager`:

        internal async Task DownloadFileAsync(MobileServiceFile file)
        {
            var todoItem = await todoTable.LookupAsync(file.ParentId);
            IPlatform platform = DependencyService.Get<IPlatform>();

            string filePath = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name); 
            await platform.DownloadFileAsync(this.todoTable, file, filePath);
        }

        internal async Task<MobileServiceFile> AddImage(TodoItem todoItem, string imagePath)
        {
            string targetPath = await FileHelper.CopyTodoItemFileAsync(todoItem.Id, imagePath);
            return await this.todoTable.AddFileAsync(todoItem, Path.GetFileName(targetPath));
        }

        internal async Task DeleteImage(TodoItem todoItem, MobileServiceFile file)
        {
            await this.todoTable.DeleteFileAsync(file);
        }

        internal async Task<IEnumerable<MobileServiceFile>> GetImageFilesAsync(TodoItem todoItem)
        {
            return await this.todoTable.GetFilesAsync(todoItem);
        }

###Agregar una vista de detalles

En esta sección, agregará una nueva vista de detalles para un elemento de tareas pendientes. La vista se crea cuando el usuario selecciona un elemento de tareas pendientes y permite que se agreguen nuevas imágenes a un elemento.

1. Agregue una nueva clase **TodoItemImage** al proyecto de biblioteca portable con la siguiente implementación:

        public class TodoItemImage : INotifyPropertyChanged
        {
            private string name;
            private string uri;

            public MobileServiceFile File { get; private set; }

            public string Name
            {
                get { return name; }
                set
                {
                    name = value;
                    OnPropertyChanged(nameof(Name));
                }
            }

            public string Uri
            {
                get { return uri; }      
                set
                {
                    uri = value;
                    OnPropertyChanged(nameof(Uri));
                }
            }

            public TodoItemImage(MobileServiceFile file, TodoItem todoItem)
            {
                Name = file.Name;
                File = file;

                FileHelper.GetLocalFilePathAsync(todoItem.Id, file.Name).ContinueWith(x => this.Uri = x.Result);
            }

            public event PropertyChangedEventHandler PropertyChanged;

            private void OnPropertyChanged(string propertyName)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }
        }

2. Edite **App.cs**. Reemplace la inicialización de `MainPage` por lo siguiente:
    
        MainPage = new NavigationPage(new TodoList());

3. En **App.cs**, agregue la siguiente propiedad:

        public static object UIContext { get; set; }

4. Haga clic con el botón derecho en el proyecto de biblioteca portable y seleccione **Agregar** -> **Nuevo elemento** -> **Multiplataforma** -> **Página Xaml de Forms**. Asigne a la vista el nombre `TodoItemDetailsView`.

5. Abra **TodoItemDetailsView.xaml** y reemplace el cuerpo de ContentPage por lo siguiente:

          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Button Clicked="OnAdd" Text="Add image"></Button>
            <ListView x:Name="imagesList"
                      ItemsSource="{Binding Images}"
                      IsPullToRefreshEnabled="false"
                      Grid.Row="2">
              <ListView.ItemTemplate>
                <DataTemplate>
                  <ImageCell ImageSource="{Binding Uri}"
                             Text="{Binding Name}">
                  </ImageCell>
                </DataTemplate>
              </ListView.ItemTemplate>
            </ListView>
          </Grid>

6. Edite **TodoItemDetailsView.xaml.cs** y agregue las siguientes instrucciones using:

        using System.Collections.ObjectModel;
        using Microsoft.WindowsAzure.MobileServices.Files;

7. Reemplazar la implementación de `TodoItemDetailsView` por lo siguiente:

        public partial class TodoItemDetailsView : ContentPage
        {
            private TodoItemManager manager;

            public TodoItem TodoItem { get; set; }        
            public ObservableCollection<TodoItemImage> Images { get; set; }

            public TodoItemDetailsView(TodoItem todoItem, TodoItemManager manager)
            {
                InitializeComponent();
                this.Title = todoItem.Name;

                this.TodoItem = todoItem;
                this.manager = manager;

                this.Images = new ObservableCollection<TodoItemImage>();
                this.BindingContext = this;
            }

            public async Task LoadImagesAsync()
            {
                IEnumerable<MobileServiceFile> files = await this.manager.GetImageFilesAsync(TodoItem);
                this.Images.Clear();

                foreach (var f in files) {
                    var todoImage = new TodoItemImage(f, this.TodoItem);
                    this.Images.Add(todoImage);
                }
            }

            public async void OnAdd(object sender, EventArgs e)
            {
                IPlatform mediaProvider = DependencyService.Get<IPlatform>();
                string sourceImagePath = await mediaProvider.TakePhotoAsync(App.UIContext);

                if (sourceImagePath != null) {
                    MobileServiceFile file = await this.manager.AddImage(this.TodoItem, sourceImagePath);

                    var image = new TodoItemImage(file, this.TodoItem);
                    this.Images.Add(image);
                }
            }
        }

###Actualización de la vista principal 

Actualice la vista principal para que se abra la vista de detalles cuando se seleccione un elemento de tareas pendientes.

En **TodoList.xaml.cs**, reemplace la implementación de `OnSelected` por lo siguiente:

    public async void OnSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var todo = e.SelectedItem as TodoItem;

        if (todo != null) {
            var detailsView = new TodoItemDetailsView(todo, manager);
            await detailsView.LoadImagesAsync();
            await Navigation.PushAsync(detailsView);
        }

        todoList.SelectedItem = null;
    }

###Actualización del proyecto Android

Agregue código específico de la plataforma al proyecto Android, por ejemplo, código para descargar un archivo y usar la cámara para capturar una nueva imagen.

Este código utiliza [DependencyService](https://developer.xamarin.com/guides/xamarin-forms/dependency-service/) de Xamarin.Forms para cargar la clase correcta específica de la plataforma en tiempo de ejecución.

1. Agregue el componente **Xamarin.Mobile** al proyecto Android.

2. Agregue una nueva clase `DroidPlatform` con la siguiente implementación. Reemplace "YourNamespace" por el espacio de nombres principal del proyecto.

        using System;
        using System.IO;
        using System.Threading.Tasks;
        using Android.Content;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.Droid.DroidPlatform))]
        namespace YourNamespace.Droid
        {
            public class DroidPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var uiContext = context as Context;
                        if (uiContext != null) {
                            var mediaPicker = new MediaPicker(uiContext);
                            var photo = await mediaPicker.TakePhotoAsync(new StoreCameraMediaOptions());

                            return photo.Path;
                        }
                    }
                    catch (TaskCanceledException) {
                    }

                    return null;
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string appData = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
                    string filesPath = Path.Combine(appData, "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Edite **MainActivity.cs**. En `OnCreate`, agregue lo siguiente antes de la llamada a `LoadApplication()`:

        App.UIContext = this;

###Actualización del proyecto iOS

Agregue código específico de la plataforma al proyecto iOS.

1. Agregue el componente **Xamarin.Mobile** al proyecto iOS.

2. Agregue una nueva clase `TouchPlatform` con la siguiente implementación. Reemplace "YourNamespace" por el espacio de nombres principal del proyecto.

        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.iOS.TouchPlatform))]
        namespace YourNamespace.iOS
        {
            class TouchPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var mediaPicker = new MediaPicker();
                        var mediaFile = await mediaPicker.PickPhotoAsync();
                        return mediaFile.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string filesPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments), "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Edite **AppDelegate.cs** y quite el comentario de la llamada a `SQLitePCL.CurrentPlatform.Init()`.

###Actualización del proyecto Windows

1. Instale la extensión de Visual Studio [SQLite para Windows 8.1](http://go.microsoft.com/fwlink/?LinkID=716919). Para más información, consulte el tutorial [Habilitación de la sincronización sin conexión para la aplicación de Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md). 

2. Edite **Package.appxmanifest** y active la función **Webcam**.

3. Agregue una nueva clase `WindowsStorePlatform` con la siguiente implementación. Reemplace "YourNamespace" por el espacio de nombres principal del proyecto.

        using System;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Windows.Foundation;
        using Windows.Media.Capture;
        using Windows.Storage;
        using YourNamespace;

        [assembly: Xamarin.Forms.Dependency(typeof(WinApp.WindowsStorePlatform))]
        namespace WinApp
        {
            public class WindowsStorePlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> GetTodoFilesPathAsync()
                {
                    var storageFolder = ApplicationData.Current.LocalFolder;
                    var filePath = "TodoItemFiles";

                    var result = await storageFolder.TryGetItemAsync(filePath);

                    if (result == null) {
                        result = await storageFolder.CreateFolderAsync(filePath);
                    }

                    return result.Name; // later operations will use relative paths
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        CameraCaptureUI dialog = new CameraCaptureUI();
                        Size aspectRatio = new Size(16, 9);
                        dialog.PhotoSettings.CroppedAspectRatio = aspectRatio;

                        StorageFile file = await dialog.CaptureFileAsync(CameraCaptureUIMode.Photo);
                        return file.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
            }
        }

##Resumen

En este artículo se describe cómo utilizar la nueva compatibilidad de archivos del SDK de cliente y servidor de Azure Mobile para que funcione con el Almacenamiento de Azure.

- Cree una cuenta de almacenamiento y agregue la cadena de conexión al back-end de la aplicación móvil. Solo el back-end tiene la llave al Almacenamiento de Azure: el cliente móvil solicita un token de SAS (Firma de acceso compartido) cada vez que necesita acceder al Almacenamiento de Azure. Para más información sobre los tokens de SAS del Almacenamiento de Azure, consulte [Firmas de acceso compartido, Parte 1: Descripción del modelo SAS].

- Cree un controlador que aplique las subclases `StorageController` para controlar las solicitudes de tokens de SAS y para obtener los archivos que están asociados con un registro. De forma predeterminada, los archivos están asociados con un registro mediante el id. de registro como parte del nombre del contenedor; este comportamiento se puede personalizar especificando una implementación de `IContainerNameResolver`. También se puede personalizar la directiva de token de SAS.

- El SDK de cliente de Azure Mobile no almacena realmente los datos de archivo. En su lugar, el SDK de cliente invoca su `IFileSyncHandler`, el cual luego decide cómo (y si) los archivos se almacenan en el dispositivo local. El controlador de sincronización se registra de la manera siguiente:

        client.InitializeFileSync(new MyFileSyncHandler(), store);

      + `IFileSyncHandler.GetDataSource` se llama cuando el SDK de cliente de Azure Mobile necesita los datos de archivo (por ejemplo, como parte del proceso de carga). Esto le ofrece la posibilidad de administrar cómo (y si) los archivos se almacenan en el dispositivo local y devolver esa información cuando sea necesario.

      + `IFileSyncHandler.ProcessFileSynchronizationAction` se invoca como parte del flujo de sincronización de archivos. Se proporcionan una referencia de archivo y un valor de enumeración FileSychronizationAction para que pueda decidir cómo debe controlar su aplicación ese evento (por ejemplo, descargar automáticamente un archivo cuando se cree o actualice, eliminar un archivo del dispositivo local cuando ese archivo se elimine del servidor, etc.).

- Un elemento `MobileServiceFile` se puede usar en modo en línea o sin conexión, mediante `IMobileServiceTable` o `IMobileServiceSyncTable`, respectivamente. En el escenario sin conexión, la carga se produce cuando la aplicación llama a `PushFileChangesAsync`. Esto hace que se procese la cola de la operación sin conexión; para cada operación de archivo, el SDK de cliente de Azure Mobile invocará el método `GetDataSource` en la instancia `IFileSyncHandler` para recuperar el contenido del archivo para la carga.

- Para recuperar archivos de un elemento, llame al método '`GetFilesAsync` en la instancia `IMobileServiceTable<T>` o IMobileServiceSyncTable<T>'. Este método devuelve una lista de archivos asociados con el elemento de datos proporcionado. (Nota: Se trata de una operación *local* y los archivos se devolverán en función del estado del objeto cuando se sincronizó por última vez. Para obtener una lista actualizada de archivos del servidor, debe iniciar primero una operación de sincronización).

        IEnumerable<MobileServiceFile> files = await myTable.GetFilesAsync(myItem);

- La característica de sincronización de archivos usa notificaciones de cambio de registro en el almacén local para recuperar los registros que recibió el cliente como parte de una operación de inserción o de extracción. Esto se logra mediante la activación de notificaciones locales y del servidor en el contexto de sincronización mediante el parámetro `StoreTrackingOptions`.

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

      + Existen también otras opciones de seguimiento de almacén, como notificaciones solo locales o solo de servidor. Puede agregar o personalizar su propia devolución de llamada mediante la propiedad `EventManager` de `IMobileServiceClient`:

            jobService.MobileService.EventManager.Subscribe<StoreOperationCompletedEvent>(StoreOperationEventHandler);

- Es posible agregar o quitar archivos de un registro modificando directamente el almacenamiento de blobs, ya que la asociación se logra a través de una convención de nomenclatura. Sin embargo, en este caso debe **actualizar siempre la marca de tiempo del registro cuando se modifiquen los blobs asociados**. El SDK de cliente de Azure Mobile siempre actualiza un registro al agregar o quitar un archivo.

    El motivo de este requisito es que algunos clientes móviles ya tendrán el registro en el almacenamiento local. Cuando estos clientes realicen un extracción incremental, este registro no se devolverá y el cliente no consultará los nuevos archivos asociados. Para evitar este problema, se recomienda que actualice la marca de tiempo del registro al realizar un cambio en el almacenamiento de blobs que no use el SDK de cliente de Azure Mobile.

- El proyecto de cliente sigue el patrón [Xamarin.Forms DependencyService] para cargar la clase correcta específica de la plataforma en tiempo de ejecución. En este ejemplo, definimos una interfaz `IPlatform` con implementaciones en cada uno de los proyectos específicos de la plataforma.

<!-- URLs. -->

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
[Creación de una aplicación Xamarin.Forms]: ../app-service-mobile-xamarin-forms-get-started.md
[Xamarin.Forms DependencyService]: https://developer.xamarin.com/guides/xamarin-forms/dependency-service/
[Microsoft.Azure.Mobile.Client.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.Files/
[Microsoft.Azure.Mobile.Client.SQLiteStore]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Microsoft.Azure.Mobile.Server.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Files/
[Firmas de acceso compartido, Parte 1: Descripción del modelo SAS]: https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/
[Crear una cuenta de almacenamiento de Azure]: https://azure.microsoft.com/es-ES/documentation/articles/storage-create-storage-account/#create-a-storage-account

<!---HONumber=AcomDC_0128_2016-->