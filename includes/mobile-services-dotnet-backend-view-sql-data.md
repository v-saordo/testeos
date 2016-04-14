
El último paso opcional de este tutorial es comprobar en la Base de datos SQL asociada con el servicio móvil una revisión de los datos almacenados.

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en la opción Administrar correspondiente a la base de datos asociada con el servicio móvil.
 
	![iniciar sesión para administrar la Base de datos SQL](./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png)

2. En el Portal ejecute una consulta para ver los cambios que realiza la aplicación de la Tienda Windows. La consulta será similar a la siguiente, pero con el nombre de su base de datos en lugar de <code>todolist</code>.</p>

        SELECT * FROM [todolist].[todoitems]

    ![consultar en la Base de datos SQL los elementos almacenados](./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png)

	La tabla incluye las columnas Id, \_\_createdAt, \_\_updatedAt y \_\_version. Estas columnas admiten la sincronización de datos sin conexión y se implementan en la clase base [EntityData](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx). Para más información, vea [Introducción a la sincronización de datos sin conexión].

<!---HONumber=AcomDC_1203_2015-->