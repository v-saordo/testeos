<properties
	pageTitle="Uso de la eliminación temporal en Servicios móviles (Tienda Windows) | Microsoft Azure"
	description="Aprenda a usar la característica de eliminación temporal de Servicios móviles de Azure en su aplicación."
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="wesmc"/>

# Uso de la eliminación temporal en Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


##Información general

Las tablas creadas con el back-end de JavaScript o de .NET pueden tener habilitada opcionalmente la eliminación temporal. Al usar la eliminación temporal, una nueva columna llamada *\_\_deleted* de [tipo de bit de SQL] se agrega a la base de datos. Con la eliminación temporal habilitada, una operación de eliminación no elimina físicamente filas de la base de datos, sino que establece el valor de la columna eliminada en TRUE.

Al consultar registros en una tabla con la eliminación temporal habilitada, las filas eliminadas no se devuelven de forma predeterminada en la consulta. Para solicitar estas filas, debe pasar un parámetro de consulta *\_\_includeDeleted=true* en la [operación de consulta REST](http://msdn.microsoft.com/library/azure/jj677199.aspx). En el SDK de cliente para .NET, también puede usar el método auxiliar `IMobileServiceTable.IncludeDeleted()`.

La compatibilidad con la eliminación temporal en el back-end de .NET se publicó por primera vez en la versión 1.0.402 del back-end de .NET de Servicios móviles de Microsoft Azure. Los últimos paquetes de NuGet están disponibles aquí, [Back-end de .NET de Servicios móviles de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=513165).


Estos son algunas de las posibles ventajas de usar la eliminación temporal:

* Al usar la característica [Sincronización de datos sin conexión para Servicios móviles], el SDK de cliente consulta automáticamente los registros eliminados y los elimina de la base de datos local. Cuando la eliminación temporal no está habilitada, debe escribir código adicional en el back-end para que el SDK de cliente sepa qué registros eliminar del almacén local. De lo contrario, el almacén local del cliente y el back-end serán incoherentes con respecto a estos registros eliminados y será necesario llamar al método del cliente `PurgeAsync()` para borrar el almacén local.
* Algunas aplicaciones tienen un requisito empresarial de no eliminar nunca los datos físicamente o de eliminar los datos solo después de que se han auditado. La característica de eliminación temporal puede ser útil en este escenario.
* La eliminación temporal puede utilizarse para implementar una característica de "recuperación" para que se pueden recuperar los datos eliminados accidentalmente. Sin embargo, los registros eliminados temporalmente ocupan espacio en la base de datos, por lo que debe considerar la creación de un trabajo programado para eliminar definitivamente periódicamente los registros eliminados temporalmente. Para ver un ejemplo de esto, consulte [Uso de la eliminación temporal con el back-end de .NET](#using-with-dotnet) y [Uso de la eliminación temporal con el back-end de JavaScript](#using-with-javascript). Su código de cliente debe llamar también periódicamente a `PurgeAsync()` de modo que estos registros eliminados de forma permanente no permanezcan en el almacén de datos local del dispositivo.





##Habilitación de la eliminación temporal para el back-end de .NET

La compatibilidad con la eliminación temporal en el back-end de .NET se publicó por primera vez en la versión 1.0.402 del back-end de .NET de Servicios móviles de Microsoft Azure. Los últimos paquetes de NuGet están disponibles aquí, [Back-end de .NET de Servicios móviles de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=513165).

Los siguientes pasos le indican cómo habilitar la eliminación temporal para un servicio móvil para el back-end de .NET.

1. Abra el proyecto del servicio móvil de back-end de .NET en Visual Studio.
2. Haga clic con el botón derecho en el proyecto del back-end de .NET y haga clic en **Administrar paquetes de NuGet**.
3. En el cuadro de diálogo del administrador de paquetes, haga clic en **Nuget.org** debajo de las actualizaciones e instale la versión 1.0.402 o posterior de los paquetes de NuGet, [Back-end de .NET de Servicios móviles de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=513165).
3. En el Explorador de soluciones de Visual Studio, expanda el nodo **Controladores** debajo del proyecto del back-end de .NET y abra su origen de controlador. Por ejemplo, *TodoItemController.cs*.
4. En el método `Initialize()` de su controlador, pase el parámetro `enableSoftDelete: true` al constructor EntityDomainManager.

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            MobileService1Context context = new MobileService1Context();
            DomainManager = new EntityDomainManager<TodoItem>(context, Request, Services, enableSoftDelete: true);
        }


##Habilitación de la eliminación temporal para el back-end de JavaScript

Si va a crear una nueva tabla para su servicio móvil, puede habilitar la eliminación temporal en la página de creación de la tabla.

![][2]

Para habilitar la eliminación temporal en una tabla existente en el back-end de JavaScript, siga estos pasos:

1. En el [Portal de Azure clásico], haga clic en el servicio móvil. Luego, haga clic en la pestaña Datos.
2. En la página de datos, haga clic para seleccionar la tabla deseada. Luego haga clic en el botón **Habilitar eliminación temporal** de la barra de comandos. Si la tabla ya se ha habilitado para la eliminación temporal, este botón no aparecerá pero podrá ver la columna *\_\_deleted* al hacer clic en la pestaña **Examinar** o **Columnas** de la tabla.

    ![][0]

    Para deshabilitar la eliminación temporal en su tabla, haga clic en la pestaña **Columnas** y luego haga clic en la columna *\_\_deleted* y en el botón **Eliminar**.

    ![][1]

## <a name="using-with-dotnet"></a>Uso de la eliminación temporal con el back-end de .NET


El siguiente trabajo programado purga los registros eliminados temporalmente que tienen más de un mes de antigüedad:

    public class SampleJob : ScheduledJob
    {
        private MobileService1Context context;

        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor,
            CancellationToken cancellationToken)
        {
            base.Initialize(scheduledJobDescriptor, cancellationToken);
            context = new MobileService1Context();
        }

        public override Task ExecuteAsync()
        {
            Services.Log.Info("Purging old records");
            var monthAgo = DateTimeOffset.UtcNow.AddDays(-30);

            var toDelete = context.TodoItems.Where(x => x.Deleted == true && x.UpdatedAt <= monthAgo).ToArray();
            context.TodoItems.RemoveRange(toDelete);
            context.SaveChanges();

            return Task.FromResult(true);
        }
    }

Para obtener información acerca de trabajos de programación con Servicios móviles de back-end de .NET, consulte: [Programación de trabajos periódicos con Servicios móviles de back-end de JavaScript](mobile-services-dotnet-backend-schedule-recurring-tasks.md)




## <a name="using-with-javascript"></a> Uso de la eliminación temporal con el back-end de JavaScript

Los scripts de tabla se usan para agregar lógica en torno a la característica de eliminación temporal con Servicios móviles para el back-end de JavaScript.

Para detectar una solicitud de recuperación, use la propiedad "undelete" en el script de la tabla de actualización:

    function update(item, user, request) {
        if (request.undelete) { /* any undelete specific code */; }
    }
Para incluir los registros eliminados en el resultado de consulta de un script, establezca el parámetro "includeDeleted" en true:

    tables.getTable('softdelete_scenarios').read({
        includeDeleted: true,
        success: function (results) {
            request.respond(200, results)
        }
    });

Para recuperar los registros eliminados a través de una solicitud HTTP, agregue el parámetro de consulta "\_\_includedeleted=true":

    http://youservice.azure-mobile.net/tables/todoitem?__includedeleted=true

### Ejemplo de trabajo programado para purgar los registros eliminados temporalmente.

Este es un trabajo programado de ejemplo que elimina los registros que se actualizaron antes de una fecha determinada:

    function purgedeleted() {
         mssql.query('DELETE FROM softdelete WHERE __deleted=1', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
        }});
    }

Para obtener información acerca de trabajos de programación con Servicios móviles de back-end de JavaScript, consulte: [Programación de trabajos periódicos con Servicios móviles de back-end de JavaScript](mobile-services-schedule-recurring-tasks.md).





<!-- Images -->
[0]: ./media/mobile-services-using-soft-delete/enable-soft-delete-button.png
[1]: ./media/mobile-services-using-soft-delete/disable-soft-delete.png
[2]: ./media/mobile-services-using-soft-delete/enable-soft-delete-new-table.png

<!-- URLs. -->
[tipo de bit de SQL]: http://msdn.microsoft.com/library/ms177603.aspx
[Sincronización de datos sin conexión para Servicios móviles]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[Portal de Azure clásico]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0218_2016-->