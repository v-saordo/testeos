En los pasos siguientes se muestra el proceso de creación de una aplicación de Dropbox en el sitio Dropbox.com. Como el sitio Dropbox.com puede cambiar sin previo aviso, puede haber diferencias en la interfaz de usuario respecto a lo que aquí se muestra.

1. Vaya a [Dropbox developer portal](https://www.dropbox.com/developers/apps) (Portal de desarrolladores de Dropbox), haga clic en **App Console** (Consola de aplicaciones) y, después, en **Create App** (Crear aplicación).

	![Crear aplicación de Dropbox](./media/app-service-api-create-dropbox-app/dbappcreate.png)

2. Elija **Dropbox API app** (Aplicación de API de Dropbox) y configure el resto de valores.
 
	Si tiene archivos en la cuenta, las opciones de acceso a archivos que se muestran en la captura de pantalla siguiente permiten probar el acceso a la cuenta de Dropbox con una solicitud HTTP Get simple.

	El nombre de la aplicación de API de Dropbox puede ser cualquier valor aceptado en el sitio de Dropbox.

3. Haga clic en **Create app** (Crear aplicación).

	![Crear aplicación de Dropbox](./media/app-service-api-create-dropbox-app/dbapiapp.png)

	En la página siguiente se muestran los valores App key (Clave de la aplicación) y App secret (Secreto de la aplicación) (Id. de cliente y Secreto del cliente respectivamente en Azure), que se usarán para configurar el conector de Dropbox de Azure.

	Esta página también tiene un campo donde puede especificarse un URI de redirección, cuyo valor se obtendrá en la sección siguiente.

	![Crear aplicación de Dropbox](./media/app-service-api-create-dropbox-app/dbappsettings.png)

<!---HONumber=Oct15_HO3-->