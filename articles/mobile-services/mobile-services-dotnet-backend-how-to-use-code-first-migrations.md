<properties
	pageTitle="Modificación del modelo de datos de un servicio móvil back-end de .NET"
	description="En este tema se describen los inicializadores de modelo de datos y cómo realizar cambios en el modelo de datos en un servicio móvil de back-end. NET."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	writer="glenga"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="NA"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="glenga"/>

# Modificación del modelo de datos de un servicio móvil back-end de .NET

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


En este tema se muestra cómo usar Migraciones de Code First en Entity Framework para realizar cambios en el modelo de datos de una base de datos SQL de Azure existente para evitar perder datos existentes. Se asume que ya publicó el proyecto de backend .NET en Azure, que hay datos guardados en la base de datos y que los modelos de datos remoto y local están sincronizados. En este tema también se describen los inicializadores Code First predeterminados que implementan los Servicios móviles de Azure que se usan durante el desarrollo. Estos inicializadores le permiten realizar cambios de esquema fácilmente sin usar Migraciones de Code First cuando no sea necesario para conservar los datos existentes.

>[AZURE.NOTE]El nombre del esquema que se usa para prefijar las tablas en la base de datos de SQL está definido por la configuración de la aplicación MS\_MobileServiceName en el archivo web.config. Al descargar el proyecto de inicio desde el portal, este valor ya está establecido en el nombre del servicio móvil. Cuando el nombre del esquema coincide con el servicio móvil, varios servicios móviles pueden compartir la misma instancia de base de datos con seguridad.

Tenga en cuenta que no se admiten migraciones automáticas en un proyecto de backend. NET.

## Actualización del modelo de datos

Cuando agrega funcionalidad para el servicio móvil de back-end de .NET, agrega nuevos controladores para exponer los nuevos extremos en la API. Crea una nueva API como un controlador personalizado o un controlador de tabla. Un [controlador de tabla<TEntity>] expone un tipo de datos que se hereda de [EntityData]. Para habilitar los datos que se conservan en la base de datos, debe agregarse este tipo de datos también al modelo de datos como un nuevo [DbSet<T>] en [DbContext]. Para obtener más información acerca de Code First en Entity Framework, consulte [Creación de un modelo con Code First](https://msdn.microsoft.com/data/ee712907#codefirst).

Visual Studio facilita la creación de un nuevo controlador de tabla para exponer un nuevo tipo de datos a las aplicaciones cliente. Para obtener más información, consulte [Uso de controladores para acceder a los datos de Servicios móviles](https://msdn.microsoft.com/library/windows/apps/xaml/dn614132.aspx).

## Inicializadores de modelos de datos

Servicios móviles proporciona dos clases base de inicializador de modelo de datos en un proyecto de servicio móvil de back-end de .NET. Ambos inicializadores eliminan y vuelven a crear tablas en la base de datos cuando Entity Framework detecta un cambio de modelo de datos en el [DbContext]. Estos inicializadores están diseñados para funcionar tanto si el servicio móvil se ejecuta en un equipo local y como si se hospeda en Azure.

>[AZURE.NOTE]Al publicar un servicio móvil de back-end. NET, el inicializador no se ejecuta hasta que se produzca una operación de acceso a datos. Esto significa que para un servicio publicado recientemente, las tablas de datos que se usan para el almacenamiento no se crean hasta que el cliente solicita una operación de acceso a datos, por ejemplo, una consulta.
>
>También puede ejecutar una operación de acceso a datos mediante el uso de la funcionalidad integrada de Ayuda de API, a la que se obtiene acceso desde el vínculo **Probarlo** en la página de inicio. Para obtener más información sobre el uso de las páginas de API para probar el servicio móvil, vea la sección Prueba del proyecto de servicio móvil localmente en [Incorporación de Servicios móviles a una aplicación existente](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md#test-the-service-locally).

Ambos inicializadores eliminan de la base de datos todas las tablas, vistas, funciones y procedimientos del esquema que usa el servicio móvil.

+ Los objetos de esquema **ClearDatabaseSchemaIfModelChanges** <br/> solo se eliminan cuando Code First detecta un cambio en un modelo de datos. El inicializador predeterminado de un proyecto de back-end de .NET descargado del [Portal de Azure clásico] hereda de esta clase base.

+ **ClearDatabaseSchemaAlways**: <br/> los objetos de esquema se eliminan cada vez que se accede al modelo de datos. Use esta clase base para restablecer la base de datos sin tener que realizar una modificación del modelo de datos.

Puede usar otros inicializadores del modelo de datos de Code First cuando se ejecuta en un equipo local. No obstante, se producirá un error en Azure de los inicializadores que intenten eliminar la base de datos porque el usuario no cuenta con los permisos para eliminar la base de datos, lo que es un comportamiento deseado.

Puede seguir utilizando los inicializadores durante el desarrollo local del servicio móvil. Además, en los tutoriales de backend .NET se presupone que está utilizando inicializadores. Sin embargo, en aquellos casos en que desee realizar cambios en el modelo de datos y conservar los datos existentes en la base de datos, debe utilizar Migraciones de Code First.

>[AZURE.IMPORTANT]A la hora de desarrollar y probar el proyecto de servicio móvil con los servicios de Azure en directo, debe utilizar siempre una instancia del servicio móvil destinada a pruebas. No debe utilizar nunca un servicio móvil que se encuentre en fase de producción o que esté siendo utilizado por aplicaciones cliente.

En el proyecto de inicio rápido descargado, el inicializador Code First se define en el archivo WebApiConfig.cs. Reemplace el método **Seed** para agregar filas de datos iniciales a nuevas tablas. Para obtener ejemplos de la propagación de datos, consulte [Propagación de datos en migraciones].

## <a name="migrations"></a>Habilitación de migraciones de Code First

Migraciones de Code First utiliza un método de instantáneas para generar código que, al ejecutarse, realiza cambios en el esquema de la base de datos. Con Migraciones, puede realizar cambios incrementales en el modelo de datos y conservar los datos existentes en la base de datos.

>[AZURE.NOTE]Si ya publicó el proyecto de servicio móvil backend .NET en Azure y el esquema de tablas de la base de datos SQL no concuerda con el modelo de datos actual del proyecto, debe usar un inicializador, eliminar las tablas manualmente o sincronizar el esquema y el modelo de datos antes de intentar la publicación mediante Migraciones de Code First.

Los siguientes pasos sirven para activar las Migraciones y aplicar los cambios en el modelo de datos en el proyecto, la base de datos local y Azure.

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto de servicio móvil y luego en **Establecer como proyecto de inicio**.

2. En el menú **Herramientas**, expanda **Administrador de paquetes NuGet** y luego haga clic en **Consola del Administrador de paquetes**.

	De este modo, se muestra la Consola del Administrador de paquetes, que se puede utilizar para administrar las Migraciones de Code First.

3. En la Consola del Administrador de paquetes, ejecute el siguiente comando:

		PM> Enable-Migrations

	De este modo, se activan las Migraciones de Code First para el proyecto.

4. En la consola, ejecute el siguiente comando:

		PM> Add-Migration Initial

	De esta forma, se crea una nueva migración denominada *Initial*. El código de la migración se almacena en la carpeta de proyecto de Migraciones.

5. Expanda la carpeta App\_Start, abra el archivo de proyecto WebApiConfig.cs y agregue las siguientes instrucciones **using**:

		using System.Data.Entity.Migrations;
		using todolistService.Migrations;

	En el código anterior, debe reemplazar la cadena _todolistService_ por el espacio de nombres del proyecto que, en el caso del proyecto de inicio rápido descargado, es <em>nombre&#95;servicio&#95;móvil</em>Service.

6. En este mismo archivo de código, convierta en comentario la llamada al método **Database.SetInitializer** y agregue el código siguiente a continuación:

        var migrator = new DbMigrator(new Configuration());
        migrator.Update();

	De este modo, se deshabilita el inicializador de base de datos de Code First predeterminado que elimina y vuelve a crear la base de datos y se reemplaza por una solicitud explícita de aplicación de la migración más reciente. En este momento, cualquier cambio en el modelo de datos provocará una excepción InvalidOperationException al obtener acceso a los datos, a menos que se cree una migración para él. A partir de ahora, el servicio debe utilizar Migraciones de Code First para migrar los cambios en el modelo de datos a la base de datos.

7.  Presione F5 para iniciar el proyecto de servicio móvil en el equipo local.

	En este punto, la base de datos está sincronizada con el modelo de datos. Si proporcionó datos de inicialización, puede comprobarlo haciendo clic en **Probarlo**, **GET tables/todoitem**, luego en **Probar esto** y seguidamente en **Enviar**. Para obtener más información, vea [Datos de inicialización en las migraciones].

8.   Ahora haga un cambio en el modelo de datos, como agregar una nueva propiedad UserId al tipo TodoItem, vuelva a compilar el proyecto y, a continuación, en el Administrador de paquetes ejecute el siguiente comando:

		PM> Add-Migration NewUserId

	De esta forma, se crea una nueva migración denominada *NewUserId*. Se agrega un nuevo archivo de código, que implementa este cambio, a la carpeta de migraciones.

9.  Presione F5 de nuevo para reiniciar el proyecto de servicio móvil en el equipo local.

	La migración se aplica a la base de datos, que vuelve a estar sincronizada con el modelo de datos. Si proporcionó datos de inicialización, puede comprobarlo haciendo clic en **Probarlo**, **GET tables/todoitem**, luego en **Probar esto** y seguidamente en **Enviar**. Para obtener más información, vea [Datos de inicialización en las migraciones].

10. Vuelva a publicar el servicio móvil en Azure, y, a continuación, ejecute la aplicación cliente para obtener acceso a los datos y compruebe que estos se cargan sin errores.

13. (Opcional) En el [Portal de Azure clásico], seleccione el servicio móvil, haga clic en **Configurar** > **Base de datos SQL**. De este modo, se abre la página de la base de datos SQL del servicio móvil.

14. (Opcional) Haga clic en **Administrar**, inicie sesión en su servidor de Base de datos SQL, haga clic en **Diseñar** y compruebe que se han realizado los cambios en el esquema en Azure.


## Uso de migraciones de Code First sin un inicializador
Antes de usar migraciones de Code First con su proyecto de backend .NET, debe ejecutar un inicializador de modelo de datos. Cuando NO se usa un inicializador, pueden producirse errores al intentar aplicar las migraciones. Si decide no usar uno de los inicializadores del modelo de datos predefinidos, asegúrese de que las migraciones estén configuradas para usar el EntityTableSqlGenerator como el SqlGenerator en el archivo Migrations\\Configuration.cs, como en el ejemplo siguiente:

    public Configuration()
    {
        AutomaticMigrationsEnabled = false;
        SetSqlGenerator("System.Data.SqlClient", new EntityTableSqlGenerator());
    }

##<a name="seeding"></a>Datos de inicialización en las migraciones

Puede hacer que Migraciones agregue datos de inicialización a la base de datos cuando se ejecuta una migración. La clase **Configuration** incluye un método **Seed** que puede reemplazar para insertar o actualizar datos. El archivo de código Configuration.cs se agrega a la carpeta de migraciones cuando están habilitadas las Migraciones. Estos ejemplos muestran cómo reemplazar el método [Seed] para propagar datos a la tabla **TodoItems**. Se llama al método [Seed] una vez realizada la migración a la versión más reciente.

###Propagación de una nueva tabla

El siguiente código propaga la tabla **TodoItems** con nuevas filas de datos:

        List<TodoItem> todoItems = new List<TodoItem>
        {
            new TodoItem { Id = "1", Text = "First item", Complete = false },
            new TodoItem { Id = "2", Text = "Second item", Complete = false },
        };

        foreach (TodoItem todoItem in todoItems)
        {
            context.Set<TodoItem>().Add(todoItem);
        }
        base.Seed(context);

###Propagar una nueva columna de una tabla

El código siguiente únicamente propaga la columna UserId:

        context.TodoItems.AddOrUpdate(
            t => t.UserId,
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 2 }
            );
        base.Seed(context);

Este código llama al método auxiliar de extensión [AddOrUpdate] para agregar datos de inicialización a la nueva columna UserId. Al utilizar [AddOrUpdate], no se crean filas duplicadas.

<!-- Anchors -->
[Migrations]: #migrations
[Datos de inicialización en las migraciones]: #seeding
[Propagación de datos en migraciones]: #seeding

<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png
[1]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png
[2]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/sql-database-drop-tables.png

<!-- URLs -->
[DropCreateDatabaseIfModelChanges]: http://msdn.microsoft.com/library/gg679604(v=vs.113).aspx
[Seed]: http://msdn.microsoft.com/library/hh829453(v=vs.113).aspx
[Portal de Azure clásico]: https://manage.windowsazure.com/
[DbContext]: http://msdn.microsoft.com/library/system.data.entity.dbcontext(v=vs.113).aspx
[AddOrUpdate]: http://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx
[controlador de tabla<TEntity>]: https://msdn.microsoft.com/library/azure/dn643359.aspx
[EntityData]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.entitydata.aspx
[DbSet<T>]: https://msdn.microsoft.com/library/azure/gg696460.aspx

<!---HONumber=AcomDC_0211_2016-->