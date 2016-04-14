

Para poder almacenar datos en su nuevo servicio móvil, debe crear antes una tabla de almacenamiento en el servicio. Estos pasos muestran cómo utilizar Visual Studio 2013 para crear una tabla en el servicio móvil. A continuación, actualizará la aplicación para usar los Servicios móviles a fin de almacenar elementos en Azure en lugar de en la colección local.


1. En el Explorador de servidores, expanda **Servicios móviles de Azure**, haga clic con el botón secundario en el servicio móvil, haga clic en **Crear tabla** y, a continuación, escriba `TodoItem` en **Nombre de la tabla**.

	![crear tabla in VS 2013](./media/mobile-services-create-new-table-vs2013/mobile-create-table-vs2013.png)

2. Expanda **Opciones avanzadas**, verifique que los permisos de funcionamiento de la tabla estén establecidos de forma predeterminada en **Cualquiera con la clave de aplicación** y, a continuación, haga clic en **Crear**.

	Esto crea la tabla TodoItem en el servidor, donde cualquiera que tenga la clave de aplicación puede tener acceso a los datos de la tabla y modificarlos sin necesidad de autenticarse antes.

	>[AZURE.NOTE]La clave de aplicación se distribuye con la aplicación. Puesto que esta clave no se distribuye de forma segura, no se puede considerar un token de seguridad. Para proteger el acceso a los datos de su servicio móvil, los usuarios se deben autenticar antes del acceso. Para obtener más información, consulte [Permisos](http://msdn.microsoft.com/library/windowsazure/jj193161.aspx).
	>
	>Las tablas nuevas se crean con las columnas Id, \_\_createdAt, \_\_updatedAt y \_\_version. Cuando está habilitado el esquema dinámico, Servicios móviles genera automáticamente columnas nuevas basadas en el objeto JSON en la solicitud de inserción o actualización. Para obtener más información, consulte [Esquema dinámico](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx).

<!---HONumber=Oct15_HO3-->