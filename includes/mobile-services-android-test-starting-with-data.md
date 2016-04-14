Ahora que la aplicación se ha actualizado para usar los Servicios móviles para almacenamiento back-end, puede probarla con los Servicios móviles usando el emulador de Android o un teléfono Android.

1. En el menú **Ejecutar**, haga clic en **Ejecutar** para iniciar el proyecto.

	De este modo se ejecuta su aplicación, que se ha creado con el SDK de Android, y se usa la biblioteca del cliente para enviar una consulta que devuelve los elementos desde su servicio móvil.

5. Como antes, escriba texto descriptivo y, a continuación, haga clic en **Agregar**.

   	Esto envía un elemento nuevo como inserción al servicio móvil.

    Puede reiniciar la aplicación para ver si los cambios se aplicaron en la base de datos de Azure. También puede examinar la base de datos mediante el Portal de Azure clásico: realice los dos pasos siguientes para ver los cambios en la base de datos.


4. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en la opción Administrar correspondiente a la base de datos asociada con el servicio móvil.

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png)

5. En el Portal de Azure clásico, ejecute una consulta para ver los cambios que realiza la aplicación de la Tienda Windows. La consulta será similar a la siguiente, pero con el nombre de la base de datos en lugar de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png)

Así concluye el tutorial **Introducción a los datos** para Android.

<!---HONumber=AcomDC_1203_2015-->