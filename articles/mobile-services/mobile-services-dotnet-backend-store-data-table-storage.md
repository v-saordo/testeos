<properties
	pageTitle="Creación de un servicio móvil back-end de .NET que usa almacenamiento de tablas | Azure Mobile Services"
	description="Obtenga información sobre cómo usar el almacenamiento de tabla de Azure con su servicio móvil de back-end de .NET."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="glenga"/>

# Creación de un servicio móvil back-end de .NET que usa almacenamiento de tablas

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

Este tema muestra cómo usar un almacén de datos no relacional para su servicio móvil de back-end de .NET. En este tutorial, modificará el proyecto de inicio rápido de Servicios móviles de Azure para usar el almacenamiento de tabla de Azure en lugar de la base de datos de Azure SQL Server predeterminada.

El tutorial requiere haber realizado el tutorial [Introducción a Servicios móviles]. También necesitará una cuenta de almacenamiento de Azure.

##Configuración del almacenamiento de tabla de Azure en el servicio móvil de back-end de .NET

En primer lugar, deberá configurar el servicio móvil y el proyecto de código de back-end de .NET para conectarse al almacenamiento de Azure.

1. En el **Explorador de soluciones** de Visual Studio, haga clic con el botón derecho en el proyecto de back-end de .NET y seleccione **Administrar paquetes NuGet**.

2. En el panel izquierdo, seleccione la categoría **En línea**, seleccione **Solo estable**, busque **MobileServices**, haga clic en **Instalar** en el paquete **Extensión de almacenamiento de Azure del back-end de .NET para Servicios móviles de Microsoft Azure** y acepte el contrato de licencia.

  	![](./media/mobile-services-dotnet-backend-store-data-table-storage/mobile-add-storage-nuget-package-dotnet.png)

  	Esto agrega compatibilidad con los servicios de almacenamiento de Azure para el proyecto de servicio móvil de back-end. de NET.

3. Si todavía no ha creado su cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento](../storage/storage-create-storage-account.md).

4. En el [Portal de Azure clásico], haga clic en **Almacenamiento**, haga clic en la cuenta de almacenamiento y haga clic en **Administrar claves**.

5. Tome nota de los valores en los campos **Nombre de cuenta de almacenamiento** y **Clave de acceso**.

6. En el servicio móvil, haga clic en la pestaña **Configurar**, vaya a **Cadenas de conexión** y escriba una nueva cadena de conexión con el **Nombre** `StorageConnectionString` y un **Valor** que sea su cadena de conexión de cuenta de almacenamiento con el formato siguiente.

		DefaultEndpointsProtocol=https;AccountName=<ACCOUNT_NAME>;AccountKey=<ACCESS_KEY>;

	![](./media/mobile-services-dotnet-backend-store-data-table-storage/mobile-blob-storage-app-settings.png)

7. En la cadena anterior, reemplace los valores de `<ACCOUNT_NAME>` y `<ACCESS_KEY>` por los valores del portal y haga clic en **Guardar**.

	La cadena de conexión de la cuenta de almacenamiento se almacena cifrada en la configuración de la aplicación. Puede acceder a ella en cualquier controlador de tabla en tiempo de ejecución.

8. En el Explorador de soluciones de Visual Studio, abra el archivo Web.config del proyecto de servicio móvil y agregue la nueva cadena de conexión:

		<add name="StorageConnectionString" connectionString="<STORAGE_CONNECTION_STRING>" />

9. Reemplace el marcador de posición `<STORAGE_CONNECTION_STRING>` por la cadena de conexión del paso 6.

	El servicio móvil usa esta cadena de conexión cuando se ejecuta en el equipo local, lo que le permite probar el código antes de publicarlo. Cuando se ejecuta en Azure, el servicio móvil usa en su lugar el valor de cadena de conexión establecido en el portal y omite la cadena de conexión del proyecto.

## <a name="modify-service"></a>Modificación de tipos de datos y controladores de tabla

Como el proyecto de inicio rápido TodoList está diseñado para trabajar con una base de datos de SQL usando Entity Framework, deberá realizar algunas actualizaciones en el proyecto para que funcione con el almacenamiento de tabla.

1. Modifique el tipo de datos de **TodoItem** para que derive de **StorageData** en lugar de **EntityData**, de la siguiente manera.

	    public class TodoItem : StorageData
	    {
	        public string Text { get; set; }
	        public bool Complete { get; set; }
	    }

	>[AZURE.NOTE]El tipo **StorageData** tiene una propiedad Id. que requiere una clave compuesta que es una cadena con el formato *partitionId*,*rowValue*.

2. En **TodoItemController**, agregue la siguiente instrucción de uso.

		using System.Web.Http.OData.Query;
		using System.Collections.Generic;

3. Reemplace el método **Initialize** de **TodoItemController** por lo siguiente.

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Create a new Azure Storage domain manager using the stored
            // connection string and the name of the table exposed by the controller.
            string connectionStringName = "StorageConnectionString";
            var tableName = controllerContext.ControllerDescriptor.ControllerName.ToLowerInvariant();
            DomainManager = new StorageDomainManager<TodoItem>(connectionStringName,
                tableName, Request, Services);
        }

	Esto crea un nuevo administrador de dominio de almacenamiento para el controlador solicitado usando la cadena de conexión de la cuenta de almacenamiento.

3. Reemplace el método **GetAllTodoItems** existente por el siguiente código.

		public Task<IEnumerable<TodoItem>> GetAllTodoItems(ODataQueryOptions options)
        {
            // Call QueryAsync, passing the supplied query options.
            return DomainManager.QueryAsync(options);
        }

	A diferencia de una base de datos SQL, esta versión no devuelve IQueryable<TEntity>, por lo que se puede enlazar al resultado pero no componerse en una consulta.

## Actualización de la aplicación de cliente

Deberá realizar un cambio en el lado del cliente para que la aplicación de inicio rápido funcione con el back-end de .NET usando el almacenamiento de tabla. Esto se debe a la clave compuesta que el proveedor de almacenamiento de tabla espera.

1. Abra el archivo de código de cliente que contiene el código de acceso a datos y busque el método donde se realiza la operación de inserción.

2. Actualice la instancia de TodoItem que se agrega para establecer explícitamente el campo Id en el formato de cadena `<partitionID>,<rowValue>`.

	Este es un ejemplo de cómo establecería este identificador en una aplicación de C#, donde la parte de la partición es fija y la parte de la fila está basada en GUID.

		 todoItem.Id = string.Format("partition,{0}", Guid.NewGuid());

Ahora ya está preparado para probar la aplicación.

## <a name="test-application"></a>Prueba de la aplicación

1. (Opcional) Vuelva a publicar el proyecto de back-end de .NET de Servicios móviles.

	También puede probar su servicio móvil localmente antes de publicar el proyecto de back-end de .NET en Azure. Tanto si prueba localmente como en Azure, el servicio móvil seguirá usando el almacenamiento de tabla de Azure.

4. Ejecute la aplicación de cliente de inicio rápido conectada al servicio móvil.

	Tenga en cuenta que no verá los elementos que agregó anteriormente con el tutorial rápido. Esto se debe a que el almacén de la tabla está vacío actualmente.

5. Agregue nuevos elementos para generar cambios en la base de datos.

	La aplicación y el servicio móvil deberán comportarse como antes, excepto que ahora los datos se almacenan en el almacén no relacional en lugar de en la base de datos de SQL.

##Pasos siguientes

Ahora que ya hemos visto lo fácil que es usar el almacenamiento de tabla con el back-end de .NET, considere la posibilidad de explorar otras opciones de almacenamiento de información de back-end:

+ [Conexión a un servidor SQL Server local mediante conexiones híbridas](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br>Las conexiones híbridas permiten que su servicio móvil se conecte de forma segura a los recursos locales. De este modo, puede hacer que los datos locales sean accesibles para los clientes móviles mediante el uso de Azure. Entre los activos admitidos se incluye cualquier recurso que se ejecute en un puerto TCP estático, como Microsoft SQL Server, MySQL, API Web HTTP y la mayoría de los servicios web personalizados.

+ [Carga de imágenes en Almacenamiento de Azure mediante Servicios móviles](mobile-services-dotnet-backend-windows-universal-dotnet-upload-data-blob-storage.md)</br>Muestra cómo ampliar el proyecto de ejemplo TodoList para poder cargar imágenes desde su aplicación al almacenamiento de blobs de Azure.

<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->


<!-- URLs. -->
[Introducción a Servicios móviles]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Portal de Azure clásico]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[MongoLab Add-on Page]: /gallery/store/mongolab/mongolab

<!---HONumber=AcomDC_0218_2016-->